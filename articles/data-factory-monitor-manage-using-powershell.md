<properties 
	pageTitle="Didacticiel : créer et surveiller une fabrique de données Azure à l'aide d'Azure PowerShell" 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# Didacticiel : Créer et surveiller une fabrique de données à l'aide d'Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

Le [prise en main Azure Data Factory][adf-get-started] didacticiel vous montre comment créer et surveiller une fabrique de données Azure à l'aide de la [portail Azure Preview][azure-preview-portal]. Dans ce didacticiel, vous créez et surveiller une fabrique de données Azure à l'aide des applets de commande PowerShell Azure. Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données à partir d'Azure blob dans une base de données SQL Azure.

> [AZURE.NOTE]Cet article ne couvre pas toutes les cmdlets de fabrique de données. Voir [référence d'applet de commande données fabrique][cmdlet-reference] pour une documentation complète sur les applets de commande de fabrique de données.

##Composants requis
Outre les conditions préalables répertoriées dans la rubrique de vue d'ensemble du didacticiel, vous devez disposer d'Azure PowerShell est installé sur votre ordinateur. Si vous n'avez pas il déjà, téléchargez et installez [Azure PowerShell][download-azure-powershell] sur votre ordinateur.

##Dans ce didacticiel
Le tableau suivant répertorie les étapes que vous allez effectuer dans le cadre de ce didacticiel et leurs descriptions.

Étape | Description
-----| -----------
[Étape 1: Créer une fabrique de données Azure](#CreateDataFactory) | Dans cette étape, vous allez créer une fabrique de données Azure nommée **ADFTutorialDataFactoryPSH**. 
[Étape 2: Créer des services liés](#CreateLinkedServices) | Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. Le StorageLinkedService lie un stockage Azure et AzureSqlLinkedService lie une base de données SQL Azure à l'ADFTutorialDataFactoryPSH.
[Étape 3: Créer d'entrée et sortie de datasets](#CreateInputAndOutputDataSets) | Dans cette étape, vous allez définir deux jeux de données (** EmpTableFromBlob ** et **EmpSQLTable**) qui sont utilisés en tant que tables d'entrée et de sortie pour le **activité copie** dans ADFTutorialPipeline que vous allez créer à l'étape suivante.
[Étape 4: Créer et exécuter un pipeline](#CreateAndRunAPipeline) | Dans cette étape, vous allez créer un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données : **ADFTutorialDataFactoryPSH**... Le pipeline aura une **activité copie** qui copie des données à partir d'Azure blob dans une table de base de données Azure de sortie.
[Étape 5: Surveiller les jeux de données et de pipeline](#MonitorDataSetsAndPipeline) | Dans cette étape, vous allez surveiller les groupes de données et le pipeline à l'aide d'Azure PowerShell dans cette étape.

## <a name="CreateDataFactory"></a>Étape 1: Créer une fabrique de données Azure
Dans cette étape, vous utilisez Azure PowerShell pour créer une fabrique de données Azure nommée **ADFTutorialDataFactoryPSH**.

1. Lancez **Azure PowerShell** et exécutez les commandes suivantes. Conservez Azure PowerShell ouverte jusqu'à la fin de ce didacticiel. Si vous fermez et rouvrez, vous devez exécuter ces commandes à nouveau.
	- Exécutez **Add-AzureAccount** et entrez le nom d'utilisateur et un mot de passe que vous utilisez pour vous connecter au portail Azure Preview.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements pour ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l'abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure en version préliminaire. 
2. Basculez vers **AzureResourceManager** mode comme les applets de commande Azure Data Factory sont disponibles dans ce mode.

		Switch-AzureMode AzureResourceManager 
3. Créer un groupe de ressources Azure nommé : **ADFTutorialResourceGroup** en exécutant la commande suivante.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Si vous utilisez un groupe de ressources différent, vous devez utiliser à la place de ADFTutorialResourceGroup dans ce didacticiel. 
4. Exécuter le **New-AzureDataFactory** applet de commande pour créer une fabrique de données nommée : **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez une erreur : **nom d'usine de données « ADFTutorialDataFactoryPSH » n'est pas disponible**, modifiez le nom (par exemple, yournameADFTutorialDataFactoryPSH). Utilisez ce nom ADFTutorialFactoryPSH lors de l'exécution des étapes de ce didacticiel.

## <a name="CreateLinkedServices"></a>Étape 2: Créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un stockage Azure, base de données SQL Azure ou une base de données locale SQL Server qui contient les données d'entrée ou stocke les données de sortie d'un pipeline de fabrique de données. Un service de calcul est le service qui traite les données d'entrée et génère des données de sortie.

Dans cette étape, vous allez créer deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. StorageLinkedService liées à des liens de service un compte Azure Storage et AzureSqlLinkedService lie une base de données SQL Azure à l'usine de données : **ADFTutorialDataFactoryPSH**. Vous allez créer un pipeline plus loin dans ce didacticiel qui copie les données à partir d'un conteneur d'objets blob StorageLinkedService dans une table SQL AzureSqlLinkedService.

### Créer un service lié pour un compte de stockage Azure
1.	Créez un fichier JSON nommé **StorageLinkedService.json** dans les **C:\ADFGetStartedPSH** avec le contenu suivant. Créez le dossier ADFGetStartedPSH si elle n'existe pas déjà.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	Dans le **Azure PowerShell**, basculez vers le **ADFGetStartedPSH** dossier. 
3.	Vous pouvez utiliser la **New-AzureDataFactoryLinkedService** applet de commande pour créer un service lié. Cette applet de commande et d'autres applets de commande Data Factory vous utiliser dans ce didacticiel vous obliger à passer des valeurs le **ResourceGroupName** et **DataFactoryName** paramètres. Vous pouvez également utiliser **Get-AzureDataFactory** pour obtenir un objet DataFactory et passez l'objet sans avoir à taper ResourceGroupName et DataFactoryName chaque fois que vous exécutez une applet de commande. Exécutez la commande suivante pour affecter le résultat de la **Get-AzureDataFactory** une variable de l'applet de commande : **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	Exécutez maintenant le **New-AzureDataFactoryLinkedService** applet de commande pour créer le service lié : **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si vous n'aviez pas exécuter le **Get-AzureDataFactory** applet de commande et affecté la sortie vers **$df** variable, vous devez spécifier des valeurs pour les paramètres ResourceGroupName et DataFactoryName comme suit.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Si vous fermez Azure PowerShell au milieu du didacticiel, vous allez avoir exécuter l'applet de commande Get-AzureDataFactory prochain lancement d'Azure PowerShell pour suivre ce didacticiel.

### Créer un service lié pour une base de données SQL Azure
1.	Créez un fichier JSON nommé AzureSqlLinkedService.json avec le contenu suivant.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Exécutez la commande suivante pour créer un service lié. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]Vérifiez que le **Autoriser l'accès aux services Azure** paramètre est activé pour votre serveur SQL Azure. Pour vérifier et l'activer, procédez comme suit :
	>
	> <ol>
<li>Cliquez sur <b>Parcourir</b> concentrateur à gauche et cliquez sur <b>serveurs SQL</b>.</li>
<li>Sélectionnez votre serveur, puis cliquez sur <b>paramètres</b> sur la <b>SQL SERVER</b> lame.</li>
<li>Dans le <b>paramètres</b> lame, cliquez sur <b>pare-feu</b>.</li>
<li>Dans le <b>paramètres Firewalll</b> lame, cliquez sur <b>ON</b> pour <b>Autoriser l'accès aux services Azure</b>.</li>
<li>Cliquez sur <b>ACTIVE</b> concentrateur à gauche pour basculer vers le <b>Data Factory</b> lame vous étiez.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>Étape 3: Créer d'entrée et sortie de tables

Dans l'étape précédente, vous avez créé des services liés **StorageLinkedService** et **AzureSqlLinkedService** pour lier un compte de stockage Azure et la base de données SQL Azure à l'usine de données : **ADFTutorialDataFactoryPSH**. Dans cette étape, vous allez créer les tables qui représentent l'entrée et de sortie des données pour l'activité de copie dans le pipeline, que vous allez créer à l'étape suivante.

Une table est un jeu de données rectangulaire et il s'agit du seul type de jeu de données qui est pris en charge pour l'instant. La table d'entrée dans ce didacticiel fait référence à un conteneur d'objets blob dans le stockage Azure qui StorageLinkedService pointe vers et la table de sortie dans une table SQL dans la base de données SQL Azure vers laquelle pointe AzureSqlLinkedService.

### Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
Ignorez cette étape si vous avez suivi le didacticiel de [prise en main Azure Data Factory][adf-get-started] l'article.

Vous devez effectuer les étapes suivantes pour préparer le stockage d'objets blob Azure et la base de données SQL Azure pour ce didacticiel.
 
* Créer un conteneur d'objets blob nommé **adftutorial** dans Azure stockage d'objets blob qui **StorageLinkedService** pointe vers. 
* Créer et télécharger un fichier texte, **emp.txt**, comme un objet blob à la **adftutorial** conteneur. 
* Créez une table nommée **emp** dans la base de données SQL Azure dans SQL Azure de base de données qui **AzureSqlLinkedService** pointe vers.


1. Lancez le bloc-notes, collez le texte suivant et enregistrez-le en tant que **emp.txt** à **C:\ADFGetStartedPSH** dossier sur votre disque dur. 

        John, Doe
		Jane, Doe
				
2. Utiliser des outils tels que [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) pour créer le **adftutorial** conteneur et pour télécharger le **emp.txt** fichier au conteneur.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Utiliser le script SQL suivant pour créer le **emp** table dans votre base de données SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Si vous avez installé sur votre ordinateur SQL Server 2014 : suivez les instructions de [étape 2: se connecter à la base de données SQL de la gestion de base de données SQL à l'aide de SQL Server Management Studio][sql-management-studio] article pour vous connecter à votre serveur SQL Azure et exécutez le script SQL.

	Si vous avez Visual Studio 2013 est installé sur votre ordinateur : dans le portail Azure Preview ([http://portal.azure.com](http://portal.sazure.com)), cliquez sur **Parcourir** hub sur la gauche, cliquez sur **serveurs SQL**, sélectionnez votre base de données, puis cliquez sur **ouvert dans Visual Studio** dans la barre d'outils pour vous connecter à votre serveur SQL Azure et exécutez le script. Si votre client n'est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure autoriser l'accès à partir de votre ordinateur (adresse IP). Consultez l'article ci-dessus pour savoir comment configurer le pare-feu pour votre serveur SQL Azure.
		
### Créer une table d'entrée 
Une table est un jeu de données rectangulaire qui dispose d'un schéma. Dans cette étape, vous allez créer une table nommée **EmpBlobTable** qui pointe vers un conteneur d'objets blob dans le stockage Azure représenté par le **StorageLinkedService** liés de service. Ce conteneur d'objets blob (** adftutorial **) contient les données d'entrée dans le fichier : **emp.txt**.

1.	Créez un fichier JSON nommé **EmpBlobTable.json** dans les **C:\ADFGetStartedPSH** dossier avec le contenu suivant :

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
	                "waitonexternal": {}
	                }
	        }
		}
	
	Notez les points suivants :
	
	- emplacement **type** a **AzureBlobLocation**.
	- **linkedServiceName** a **StorageLinkedService**. 
	- **folderPath** est défini sur le **adftutorial** conteneur. Vous pouvez également spécifier le nom d'un objet blob dans le dossier. Étant donné que vous ne spécifiez pas le nom de l'objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme données d'entrée.  
	- format **type** est définie sur **FormatTexte**
	- Il existe deux champs dans le fichier texte – **FirstName** et **LastName** – séparés par une virgule (** columnDelimiter **)	
	- Le **disponibilité** est définie sur **toutes les heures** (** fréquence ** a la valeur **heure** et **intervalle** est définie sur **1** ), de sorte que le service de données fabrique recherchera les données d'entrée toutes les heures dans le dossier racine dans le conteneur d'objets blob (** adftutorial **) vous avez spécifié.

	Si vous ne spécifiez pas un **nom de fichier** pour un **d'entrée** **table**, tous les fichiers BLOB à partir du dossier d'entrée (** folderPath **) sont considérés comme entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour obtenir des exemples.
 
	Si vous ne spécifiez pas un **nom de fichier** pour une **table de sortie**, le généré les fichiers dans le **folderPath** sont nommées selon le format suivant : données. < Guid > .txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2.	Exécutez la commande suivante pour créer la table de la fabrique de données.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Créer la table de sortie
Dans cette partie de l'étape, vous allez créer une table de sortie nommée **EmpSQLTable** qui pointe vers une table SQL (** emp **) dans la base de données SQL Azure qui est représenté par le **AzureSqlLinkedService** liés de service. Le pipeline de copie des données à partir de l'objet blob d'entrée à la **emp** table.

1.	Créez un fichier JSON nommé **EmpSQLTable.json** dans les **C:\ADFGetStartedPSH** dossier avec le contenu suivant.
		
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
	* **linkedServiceName** a **AzureSqlLinkedService**.
	* **tablename** est définie sur **emp**.
	* Il existe trois colonnes – **ID**, **FirstName**, et **LastName** – dans la table emp dans la base de données, mais l'ID est une colonne d'identité, vous devez spécifier uniquement **FirstName** et **LastName** ici.
	* Le **disponibilité** est définie sur **toutes les heures** (** fréquence ** valeur **heure** et **intervalle** la valeur **1**). Le service de données fabrique génère une tranche de données de sortie toutes les heures dans les **emp** table dans la base de données SQL Azure.

2.	Exécutez la commande suivante pour créer la table de la fabrique de données.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Étape 4: Créer et exécuter un pipeline
Dans cette étape, vous créez un pipeline avec une **activité copie** qui utilise **EmpTableFromBlob** en tant qu'entrée et **EmpSQLTable** en tant que sortie.

1.	Créez un fichier JSON nommé **ADFTutorialPipeline.json** dans les **C:\ADFGetStartedPSH** dossier avec le contenu suivant : 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
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
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	Notez les points suivants :

	- Dans la section des activités, il n'existe qu'une seule activité dont **type** a **CopyActivity**.
	- Entrée de l'activité est définie sur **EmpTableFromBlob** et de sortie de l'activité est définie sur **EmpSQLTable**.
	- Dans le **transformation** section, **BlobSource** est spécifié comme type de source et **SqlSink** est spécifié comme type de récepteur.

	> [AZURE.NOTE]Remplacez la valeur de la **Démarrer** propriété avec la date du jour et **fin** valeur avec le jour suivant. Les deux commencer et dates/heures de fin doivent être dans [au format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. Le **fin** temps est facultative, mais nous allons l'utiliser dans ce didacticiel. Si vous ne spécifiez pas de valeur pour le **fin** propriété, il est calculé en tant que « ** début + 48 heures ** ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour le **fin** propriété. Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
	> [Référence de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	Exécutez la commande suivante pour créer la table de la fabrique de données. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

## <a name="MonitorDataSetsAndPipeline"></a>Étape 5: Surveiller les groupes de données et le pipeline
Dans cette étape, vous allez utiliser Azure PowerShell pour contrôler ce qui se passe dans une fabrique de données Azure.

1.	Exécutez **Get-AzureDataFactory** et assigner le résultat à une variable $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Exécutez **Get-AzureDataFactorySlice** pour obtenir des détails sur tous les secteurs de la **EmpSQLTable**, qui est la table de sortie du pipeline.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]Remplacez l'année, mois et partie date de la **StartDateTime** paramètre avec l'année en cours, le mois et la date. Cela doit correspondre à la **Démarrer** valeur dans le pipeline de JSON.

	Vous devez voir des 24 secteurs, un pour chaque heure de 12: 00 du jour actuel à 12: 00 le lendemain.
	
	**Premier secteur :**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Dernière tranche :**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Exécutez **Get-AzureDataFactoryRun** pour obtenir les détails d'activité s'exécute pour un **spécifique** tranche. Modifiez la valeur de la **StartDateTime** paramètre pour faire correspondre les **Démarrer** temps du secteur de la sortie ci-dessus. La valeur de la **StartDateTime** doit se trouver dans [au format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-03-03T22:00:00Z.

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

> [AZURE.NOTE]Voir [référence d'applet de commande données fabrique][cmdlet-reference] pour une documentation complète sur les applets de commande de fabrique de données.

## Étapes suivantes

Article | Description
------ | ---------------
[Copier des données avec Azure Data Factory - activité de copie][copy-activity] | Cet article fournit une description détaillée de la **activité copie** vous avez utilisé dans ce didacticiel. 
[Activer vos pipelines travailler avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas qui montre comment copier des données d'un **base de données SQL Server sur site** à un objet blob Azure. 
[Utilisez Pig et Hive avec fabrique de données][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas qui montre comment utiliser **HDInsight activité** pour exécuter une **hive/pig** script pour traiter les données d'entrée pour produire des données de sortie.
[Didacticiel : Déplacer et traiter des fichiers journaux à l'aide de la fabrique de données][adf-tutorial] | Cet article fournit une **procédure pas à pas de bout en bout** qui montre comment implémenter un **scénario réel** à l'aide de la fabrique de données Azure pour transformer les données à partir des fichiers journaux dans les analyses.
[Utiliser des activités personnalisées dans une fabrique de données][use-custom-activities] | Cet article fournit une procédure pas à pas des instructions détaillées pour la création d'un **activité personnalisée** et son utilisation dans un pipeline. 
[Résoudre les problèmes de la fabrique de données][troubleshoot] | Cet article décrit comment **dépanner** émet de la fabrique de données Azure. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence de Cmdlet fabrique de données Azure][cmdlet-reference] | Ce contenu de référence comporte des détails sur tous les **applets de commande Data Factory**.
[Référence du développeur fabrique données Azure][developer-reference] | La référence du développeur possède le contenu de référence complet pour les applets de commande, script JSON, fonctions, etc.... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: powershell-install-configure.md
[data-factory-create-sql-database]: sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir-->