<properties 
	pageTitle="Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API REST | Microsoft Azure" 
	description="Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API REST." 
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
	ms.date="08/17/2016" 
	ms.author="spelluru"/>

# Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble du didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Utilisation de Data Factory Editor](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Utiliser PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Utilisation de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Utilisation de l'API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Utilisation de l’API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Utilisation de l’Assistant de copie](data-factory-copy-data-wizard-tutorial.md)

Ce didacticiel vous montre comment créer et surveiller une fabrique de données Azure à l’aide de l’API REST. Le pipeline de la fabrique de données utilise une activité de copie pour copier les données d’un objet blob Azure vers une base de données SQL Azure.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. Elle est mise en œuvre par un service disponible dans le monde entier, capable de copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).

> [AZURE.NOTE] 
Cet article ne couvre pas toute l’API REST Data Factory. Consultez les [informations de référence sur l’API REST Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) pour obtenir une documentation complète sur les applets de commande Data Factory.
  

## Conditions préalables

- Lisez l’article [Vue d’ensemble du didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Installez [Curl](https://curl.haxx.se/dlwiz/) sur votre ordinateur. L’outil Curl et les commandes REST vous permettent de créer une fabrique de données.
- Suivez les instructions de [cet article](../resource-group-create-service-principal-portal.md) pour effectuer les opérations suivantes :
	1. Créez une application Web nommée **ADFCopyTutotiralApp** dans Azure Active Directory.
	2. Obtenez un **ID client** et une **clé secrète**.
	3. Obtenez l’**ID de locataire**.
	4. Attribuez l’application **ADFCopyTutotiralApp** au rôle **Collaborateurs de fabrique de données**.
- Installez [Azure PowerShell](../powershell-install-configure.md).
- Démarrez **PowerShell** et exécutez la commande suivante. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous fermez puis rouvrez Azure PowerShell, vous devrez réexécuter ces commandes.
	1. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.
	
			Login-AzureRmAccount   
	2. Exécutez la commande suivante pour afficher tous les abonnements de ce compte.

			Get-AzureRmSubscription 
	3. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **&lt;NameOfAzureSubscription**&gt; par le nom de votre abonnement Azure.

			Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
	1. Créez un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante dans PowerShell.

			New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

		Si le groupe de ressources existe, indiquez s’il faut le mettre à jour (Y) ou le conserver tel quel (N).

		Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé ADFTutorialResourceGroup. Si vous utilisez un autre groupe de ressources, vous devez remplacer ADFTutorialResourceGroup par le nom de votre groupe de ressources dans ce didacticiel.
  
## Créer des définitions JSON
Créez les fichiers JSON suivants dans le dossier où se trouve le fichier curl.exe.

### datafactory.json 
> [AZURE.IMPORTANT] Le nom doit être globalement unique : vous pouvez ajouter un préfixe/suffixe à ADFCopyTutorialDF pour rendre ce nom unique.

	{  
	    "name": "ADFCopyTutorialDF",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Remplacez **accountname** et **accountkey** par le nom de votre compte Azure Storage et par sa clé. Pour savoir comment obtenir votre clé d’accès de stockage, voir [Affichage, copie et régénération de clés d’accès de stockage](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] Remplacez **servername**, **databasename**, **username** et **password** par les noms de votre serveur SQL Azure, de la base de données SQL, du compte d’utilisateur et par le mot de passe de ce compte.

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}


### inputdataset.json

	{
	  "name": "AzureBlobInput",
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
	    "linkedServiceName": "AzureStorageLinkedService",
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

La définition JSON spécifie un jeu de données nommé **AzureBlobInput**, qui représente les données d’entrée correspondant à une activité du pipeline. En outre, elle indique que les données d’entrée se trouvent dans le fichier **emp.txt** qui est lui-même dans un conteneur d’objets blob **adftutorial**.

 Notez les points suivants :

- Le **type** de jeu de données est défini sur **AzureBlob**.
- **linkedServiceName** est défini sur la valeur **AzureStorageLinkedService**.
- **folderPath** est défini sur le conteneur **adftutorial** et **fileName** est défini sur **emp.txt**.
- Le **type** de format a la valeur **TextFormat**.
- Le fichier texte contient deux champs, **FirstName** et **LastName**, séparés par une virgule (**columnDelimiter**).
- L’élément **availability** est défini sur **hourly** (frequency sur hour et interval sur 1). Le service Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur d’objets blob (**adftutorial**).

Si vous ne spécifiez pas de nom (**fileName**) pour un jeu de données d’entrée, tous les fichiers/objets blob du dossier d’entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée.

Si vous ne spécifiez pas de nom (**fileName**) pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.&lt;Guid&gt;.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété **partitionedBy**. Dans l’exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],


### outputdataset.json
	
	{
	  "name": "AzureSqlOutput",
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


La définition JSON spécifie un jeu de données nommé **AzureSqlOutput**, qui représente les données de sortie correspondant à une activité du pipeline. En outre, elle indique que les résultats sont stockés dans la table : **emp** dans la base de données représentée par le service AzureSqlLinkedService. La section **availability** spécifie que le jeu de données de sortie est produit toutes les heures (fréquence : heure et intervalle : 1).

Notez les points suivants :

- Le **type** de jeu de données est défini sur **AzureSQLTable**.
- **linkedServiceName** est défini sur **AzureSqlLinkedService**.
- **tablename** est défini sur **emp**.
- La table emp de la base de données contient trois colonnes : **ID**, **FirstName** et **LastName**. ID étant une colonne d’identité, il suffit de spécifier **FirstName** et **LastName**.
- **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** est défini sur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Azure.

### pipeline.json

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
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
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
	    "start": "2016-08-12T00:00:00Z",
	    "end": "2016-08-13T00:00:00Z"
	  }
	}


Notez les points suivants :

- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **CopyActivity**.
- L’entrée de l’activité a la valeur **AzureBlobInput** et la sortie de l’activité a la valeur **AzureSqlOutput**.
- Dans la section **transformation**, **BlobSource** est spécifié en tant que type de source et **SqlSink** est spécifié en tant que type de récepteur.

Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end** par le jour suivant. Si vous le souhaitez, spécifiez uniquement la date et ignorez l'heure. For example, "2015-02-03", which is equivalent to "2015-02-03T00:00:00Z"

Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.

Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour la propriété **end**.

Dans l’exemple ci-dessus, il existe 24 tranches de données, car une tranche est générée par heure.
	
> [AZURE.NOTE] Consultez [Anatomie d’un pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) pour plus d’informations sur les propriétés JSON utilisées dans l’exemple ci-dessus.

## Définir des variables globales

Dans Azure PowerShell, exécutez les commandes suivantes après avoir remplacé les valeurs par les vôtres :

> [AZURE.IMPORTANT] Consultez la section [Configuration requise](#prerequisites) pour savoir comment obtenir un ID client, une clé secrète client, un ID de locataire et un ID d’abonnement.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "ADFCopyTutorialDF"

## S’authentifier avec AAD 
Exécutez la commande suivante pour vous authentifier auprès d’Azure Active Directory (AAD).

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 

## Créer une fabrique de données

Dans cette étape, vous créez une fabrique de données Azure nommée **ADFCopyTutorialDF**. Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline peut contenir une ou plusieurs activités. Par exemple, une activité de copie pour copier les données d’une source vers un magasin de données de destination. Une activité HDInsight Hive pour exécuter le script Hive afin de transformer les données d’entrée en données de sortie produit. Exécutez les commandes suivantes pour créer la fabrique de données :

1. Attribuez la commande à une variable nommée **cmd**.

	Vérifiez que le nom de la fabrique de données que vous spécifiez ici (ADFCopyTutorialDF) correspond au nom spécifié dans **datafactory.json**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Affichez les résultats. Si la fabrique de données a été créée avec succès, le code JSON de la fabrique de données apparaît dans les **résultats**. Dans le cas contraire, un message d’erreur apparaît.

		$results

Notez les points suivants :
 
- Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur : **Le nom de fabrique de données « ADFCopyTutorialDF » n’est pas disponible** apparaît dans les résultats, procédez comme suit :
	1. Modifiez le nom (par exemple, votrenomADFCopyTutorialDF) dans le fichier **datafactory.json**.
	2. Dans la première commande où une valeur est attribuée à la variable **$cmd**, remplacez ADFCopyTutorialDF par le nouveau nom, puis exécutez la commande.
	3. Exécutez les deux commandes suivantes pour appeler l’API REST afin de créer la fabrique de données et d’imprimer les résultats de l’opération.
	
	Consultez la rubrique [Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
- Pour créer des instances de fabrique de données, vous devez avoir le statut d’administrateur/collaborateur de l’abonnement Azure
- Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.
- Si vous recevez le message d’erreur : « **L’abonnement n’est pas inscrit pour utiliser l’espace de noms Microsoft.DataFactory** », effectuez l’une des opérations suivantes et essayez de relancer la publication :

	- Dans Azure PowerShell, exécutez la commande suivante pour enregistrer le fournisseur Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Vous pouvez exécuter la commande suivante pour confirmer que le fournisseur Data Factory est bien enregistré.
	
			Get-AzureRmResourceProvider
	- Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de l’abonnement Azure et accédez à un panneau Data Factory (ou) créez une fabrique de données dans le portail Azure. Cette action enregistre automatiquement le fournisseur.

Avant de créer un pipeline, vous devez d’abord créer quelques entités de la fabrique de données. Vous créez d’abord les services liés pour lier les magasins de données source et de destination à votre magasin de données. Ensuite, définissez les jeux de données d’entrée et de sortie pour représenter les données des magasins liés. Enfin, créez le pipeline avec une activité qui utilise ces jeux de données.

## Créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être un stockage Azure, une base de données SQL Azure ou une base de données SQL Server locale qui contient des données d’entrée ou qui stocke les données de sortie d’un pipeline Data Factory. Un service de calcul est un service qui traite les données d’entrée et génère des données de sortie.

Dans cette étape, vous créez deux services liés : **AzureStorageLinkedService** et **AzureSqlLinkedService**. Le service lié StorageLinkedService relie un compte Azure Storage et AzureSqlLinkedService relie une base de données Azure SQL à la fabrique de données : **ADFCopyTutorialDF**. Plus loin dans ce didacticiel, vous allez créer un pipeline qui servira à copier les données d’un conteneur d’objets blob dans AzureStorageLinkedService vers une table SQL dans AzureSqlLinkedService.

### Créer le service lié Azure Storage
Dans cette étape, vous liez votre compte Azure Storage à votre fabrique de données. Pour les besoins de ce didacticiel, vous utilisez le compte Azure Storage afin de stocker les données d’entrée.

1. Attribuez la commande à une variable nommée **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Affichez les résultats. Si le service lié a été créé avec succès, le code JSON du service lié apparaît dans les **résultats**. Dans le cas contraire, un message d’erreur apparaît.
  
		$results

### Créer un service lié Azure SQL
Dans cette étape, vous liez votre base de données SQL Azure à votre fabrique de données. Avec ce didacticiel, vous utilisez la même base de données SQL Azure pour stocker les données de sortie.

1. Attribuez la commande à une variable nommée **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Affichez les résultats. Si le service lié a été créé avec succès, le code JSON du service lié apparaît dans les **résultats**. Dans le cas contraire, un message d’erreur apparaît.
  
		$results

## Créer des jeux de données

À l’étape précédente, vous avez créé les services liés **AzureStorageLinkedService** et **AzureSqlLinkedService** pour lier un compte Azure Storage et une base de données SQL Azure à la fabrique de données **ADFCopyTutorialDF**. Dans cette étape, vous créez des jeux de données qui représentent des données d’entrée et de sortie pour l’activité de copie dans le pipeline que vous créez à l’étape suivante.

Le jeu de données d’entrée dans ce didacticiel fait référence à un conteneur d’objets blob dans le stockage Azure identifié par AzureStorageLinkedService. Le jeu de données de sortie fait référence à une table SQL de la base de données SQL Azure, identifiée par AzureSqlLinkedService.

### Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
Effectuez les étapes suivantes pour préparer le stockage d’objets blob Azure et la base de données SQL Azure pour ce didacticiel.
 
* Créez un conteneur d’objets blob nommé **adftutorial** dans le stockage d’objets blob Azure vers lequel pointe **AzureStorageLinkedService**.
* Créez et téléchargez un fichier texte, **emp.txt**, en tant qu’objet blob vers le conteneur **adftutorial**.
* Créez une table nommée **emp** dans la base de données SQL Azure vers laquelle pointe **AzureSqlLinkedService**.


1. Lancez le Bloc-notes, collez le texte suivant, puis enregistrez-le sous le nom **emp.txt** dans le dossier **C:\\ADFGetStartedPSH** sur votre disque dur.

        John, Doe
		Jane, Doe
				
2. Utilisez des outils tels que l’[Explorateur Azure Storage](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et télécharger le fichier **emp.txt** vers ce dernier.

    ![Azure Storage Explorer](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
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

	Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure afin d’autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez [cet article](../sql-database/sql-database-configure-firewall-settings.md) pour savoir comment configurer le pare-feu de votre serveur SQL Azure.
		
### Créer le jeu de données d’entrée 
Dans cette étape, vous créez un jeu de données nommé **AzureBlobInput** qui pointe vers un conteneur d’objets blob dans l’emplacement Azure Storage représenté par le service lié **AzureStorageLinkedService**. Ce conteneur d’objets blob (**adftutorial**) contient les données d’entrée dans le fichier : **emp.txt**.

1. Attribuez la commande à une variable nommée **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Affichez les résultats. Si le jeu de données a été créé avec succès, le code JSON du jeu de données apparaît dans les **résultats**. Dans le cas contraire, un message d’erreur apparaît.
  
		$results

### Créer un jeu de données de sortie
Dans cette étape, vous créez une table de sortie nommée **AzureSqlOutput**. Ce jeu de données pointe vers une table SQL (**emp**) dans la base de données SQL Azure représentée par **AzureSqlLinkedService**. Le pipeline copie les données à partir de l’objet blob d’entrée vers la table **emp**.

1. Attribuez la commande à une variable nommée **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Affichez les résultats. Si le jeu de données a été créé avec succès, le code JSON du jeu de données apparaît dans les **résultats**. Dans le cas contraire, un message d’erreur apparaît.
  
		$results 

## Création d’un pipeline
Dans cette étape, vous créez un pipeline avec une **activité de copie** qui utilise **AzureBlobInput** en entrée et **AzureSqlOutput** en sortie.

1. Attribuez la commande à une variable nommée **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Affichez les résultats. Si le jeu de données a été créé avec succès, le code JSON du jeu de données apparaît dans les **résultats**. Dans le cas contraire, un message d’erreur apparaît.

		$results

**Félicitations !** Vous avez créé une fabrique de données Azure, avec un pipeline qui copie les données de stockage d’objets blob Azure vers une base de données SQL Azure.

## Surveillance d’un pipeline
Dans cette étape, vous utilisez l’API REST Data Factory pour surveiller les tranches produites par le pipeline.

	$ds ="AzureSqlOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;


	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

Exécutez ces commandes jusqu’à ce que vous voyiez une tranche à l’état **Prêt** ou **Échec**. Lorsque la tranche est prête, vérifiez les données de sortie de la table **emp** dans votre base de données SQL Azure.

Pour chaque tranche, deux lignes de données tirées du fichier source sont copiées dans la table emp de la base de données SQL Azure. Par conséquent, 24 nouveaux enregistrements apparaissent dans la table emp lorsque toutes les tranches sont traitées avec succès (état Prêt).


## Résumé
Dans ce didacticiel, vous avez utilisé une API REST pour créer une fabrique de données Azure afin de copier des données d’objet blob Azure dans une base de données SQL Azure. Voici les étapes de premier niveau que vous avez effectuées dans ce didacticiel :

1.	Création d’une **fabrique de données** Azure.
2.	Création de **services liés** :
	1. Un service lié Azure Storage pour lier votre compte Azure Storage contenant des données d’entrée.
	2. Un service lié Azure SQL pour lier votre base de données Azure SQL contenant les données de sortie.
3.	Création des **jeux de données** qui décrivent les données d’entrée et de sortie des pipelines.
4.	Création d’un **pipeline** avec une activité de copie avec BlobSource en tant que source et SqlSink en tant que récepteur.

## Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Activités de déplacement des données](data-factory-data-movement-activities.md) | Cet article fournit une description détaillée de l’activité de copie que vous avez utilisée dans ce didacticiel. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) | Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory, et à les utiliser dans l’optique de créer des workflows pilotés par les données de bout en bout pour votre scénario ou votre entreprise. |
| [Groupes de données](data-factory-create-datasets.md) | Cet article vous aide à comprendre les jeux de données dans Azure Data Factory.
| [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) | Cet article décrit comment surveiller, gérer et déboguer les pipelines à l’aide de l’application de surveillance et gestion. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0824_2016-->