<properties
	pageTitle="Concevez votre premier pipeline en utilisant Azure Data Factory | Microsoft Azure"
	description="Ce didacticiel vous montre comment créer un pipeline de données d’exemple qui transforme des données à l’aide d’Azure HDInsight et Azure PowerShell"
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

# Concevez votre premier pipeline Azure Data Factory en utilisant Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Dans cet article, vous apprendrez à utiliser Azure PowerShell pour créer votre premier pipeline. Ce didacticiel se déroule comme suit :

1.	Création de la fabrique de données
2.	Création des services liés (magasins de données, calculs) et des jeux de données
3.	Création du pipeline

Cet article ne fournit pas une vue d'ensemble conceptuelle du service Azure Data Factory. Pour obtenir une présentation détaillée de ce service, consultez l'article [Présentation d’Azure Data Factory](data-factory-introduction.md).

## Étape 1 : création de la fabrique de données

Dans cette étape, vous utilisez Azure PowerShell pour créer une fabrique de données Azure nommée ADFTutorialDataFactoryPSH.

1. Démarrez Azure PowerShell et exécutez les commandes suivantes. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous le fermez puis le rouvrez, vous devez réexécuter ces commandes à nouveau.
	- Exécutez **Add-AzureAccount**, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure en version préliminaire.  
	- Exécutez **Get-AzureSubscription** pour afficher tous les abonnements de ce compte.
	- Exécutez **Select-AzureSubscription** pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail en version préliminaire.
2. Passez en mode AzureResourceManager, car les cmdlets Azure Data Factory sont disponibles uniquement dans ce mode.

		Switch-AzureMode AzureResourceManager
3. Créez un groupe de ressources Azure nommé *ADFTutorialResourceGroup* en exécutant la commande suivante.

		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé ADFTutorialResourceGroup. Si vous utilisez un autre groupe de ressources, vous devrez l’utiliser à la place d’ADFTutorialResourceGroup dans ce didacticiel.
4. Exécutez l’applet de commande **New-AzureDataFactory** pour créer une fabrique de données nommée DataFactoryMyFirstPipelinePSH.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

	Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche : **Le nom de la fabrique de données « DataFactoryMyFirstPipelinePSH » n’est pas disponible**, changez le nom (par exemple, votrenomADFTutorialDataFactoryPSH). Utilisez ce nom à la place d’ADFTutorialFactoryPSH quand vous effectuez les étapes de ce didacticiel.

Dans les étapes suivantes, vous apprendrez à créer les services, les jeux de données et le pipeline liés que vous utilisez dans ce didacticiel.

## Étape 2 : création des services et des jeux de données liés
Dans cette étape, vous lierez votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Vous créerez également un jeu de données pour représenter les données de sortie issues d’un traitement Hive.

### Créer le service lié Azure Storage
1.	Créez un fichier JSON nommé StorageLinkedService.json dans le dossier C:\\ADFGetStartedPSH avec le contenu suivant. Créez le dossier ADFGetStartedPSH s’il n’existe pas déjà.

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

	Remplacez **account name** par le nom de votre compte de stockage Azure et **account key** par sa clé d’accès. Pour découvrir comment obtenir votre clé d’accès de stockage, consultez la rubrique [Affichage, copie et régénération de clés d’accès de stockage](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2.	Dans Azure PowerShell, basculez vers le dossier ADFGetStartedPSH.
3.	Vous pouvez utiliser l’applet de commande **New-AzureDataFactoryLinkedService** pour créer un service lié. Cette applet de commande et d’autres applets de commande Data Factory que vous utilisez dans ce didacticiel vous obligent à transmettre des valeurs aux paramètres *ResourceGroupName* et *DataFactoryName*. Vous pouvez également utiliser **Get-AzureDataFactory** pour obtenir un objet **DataFactory** et transmettre l’objet sans avoir à taper *ResourceGroupName* et *DataFactoryName* chaque fois que vous exécutez une applet de commande. Exécutez la commande suivante pour affecter le résultat de l’applet de commande **Get-AzureDataFactory** à une variable **$df**.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	À présent, exécutez l’applet de commande **New-AzureDataFactoryLinkedService** pour créer le service lié **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si vous n’avez pas exécuté l’applet de commande **Get-AzureDataFactory** et affecté la sortie à la variable **$df**, il vous faut spécifier des valeurs pour les paramètres *ResourceGroupName* et *DataFactoryName* comme suit.

		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Si vous fermez Azure PowerShell au milieu du didacticiel, il vous faut exécuter l’applet de commande **Get-AzureDataFactory** au prochain démarrage d’Azure PowerShell pour terminer le didacticiel.

### Créer le service lié Azure HDInsight
Vous allez maintenant créer un service lié pour le cluster Azure HDInsight à la demande qui sera utilisé pour exécuter le script Hive.

1.	Créez un fichier JSON nommé HDInsightOnDemandLinkedService.json dans le dossier C:\\ADFGetStartedPSH avec le contenu suivant.


		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:05:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
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
2. Exécutez l’applet de commande **New-AzureDataFactoryLinkedService** pour créer le service lié HDInsightOnDemandLinkedService.

		New-AzureDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Créer le jeu de données de sortie
Vous allez maintenant créer le jeu de données de sortie pour représenter les données stockées dans le stockage Azure Blob.

1.	Créez un fichier JSON nommé OutputTable.json dans le dossier C:\\ADFGetStartedPSH avec le contenu suivant :

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
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

	Dans l’exemple précédent, vous créez un jeu de données appelé **AzureBlobOutput** et vous spécifiez la structure de données qui sera générée par le script Hive. En outre, vous spécifiez que les résultats sont stockés dans le conteneur d’objets blob appelé **données** et dans le dossier appelé **partitioneddata**. La section **disponibilité** spécifie que le jeu de données de sortie est généré sur une base mensuelle.

2. Exécutez la commande suivante dans Azure PowerShell pour créer la table Data Factory :

		New-AzureDataFactoryTable $df -File .\OutputTable.json

## Étape 3 : création de votre premier pipeline
Dans cette étape, vous allez créer votre premier pipeline.

1.	Créez un fichier JSON nommé MyFirstPipelinePSH.json dans le dossier C:\\ADFGetStartedPSH avec le contenu suivant :

	> [AZURE.IMPORTANT]Remplacez **storageaccountname** dans le script JSON par le nom de votre compte de stockage.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
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

	Dans l’exemple précédent, vous créez un pipeline qui se compose d’une seule activité utilisant Hive pour traiter des données sur un cluster HDInsight.

	Le fichier de script Hive, partitionweblogs.hql, est stocké dans le compte de stockage Azure (spécifié par le scriptLinkedService, appelé StorageLinkedService) et dans un conteneur appelé **script**.

	La section **extendedProperties** permet de spécifier les paramètres d’exécution qui seront transmis au script Hive en tant que valeurs de configuration Hive (par exemple ${hiveconf:PartitionedData}).

	Les propriétés **start** et **end** du pipeline spécifient la période active du pipeline.

	Dans l’activité JSON, vous spécifiez que le script Hive s’exécute sur l’ordinateur spécifié par le service lié **HDInsightOnDemandLinkedService**.
2. Exécutez la commande suivante pour créer la table Data Factory :

		New-AzureDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Félicitations, vous avez créé correctement votre premier pipeline à l'aide d'Azure PowerShell.

### <a name="MonitorDataSetsAndPipeline"></a> Surveillance des jeux de données et du pipeline
Au cours de cette étape, vous allez utiliser Azure PowerShell pour surveiller ce qui se passe dans une fabrique de données Azure.

1.	Exécutez **Get-AzureDataFactory** et affectez le résultat à une variable **$df**.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.	Exécutez **Get-AzureDataFactorySlice** pour obtenir des détails sur toutes les tranches de **EmpSQLTable**, qui est la table de sortie du pipeline.

		Get-AzureDataFactorySlice $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Notez que la valeur StartDateTime que vous spécifiez ici est identique à l’heure de début que vous avez spécifiée dans le pipeline de JSON. Le résultat ressemble à ce qui suit.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0

3.	Exécutez **Get-AzureDataFactoryRun** pour obtenir les détails d’exécution d’activité pour une tranche spécifique.

		Get-AzureDataFactoryRun $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Le résultat ressemble à ce qui suit.

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Vous pouvez continuer d’exécuter cette applet de commande jusqu'à ce que la tranche indique l’état Prêt ou Détérioré. Lorsque la tranche indique l’état Prêt, vérifiez le dossier partitioneddata du conteneur de données de votre stockage d'objets blob pour les données de sortie. Notez que la création d’un cluster HDInsight à la demande prend généralement un certain temps.

Consultez la [référence des applets de commande Data Factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) pour obtenir une documentation complète sur les applets de commande Data Factory.



## Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster Azure HDInsight à la demande. Pour apprendre à utiliser une activité de copie pour copier des données à partir d’un objet blob Azure dans Azure SQL, consultez le [didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](./data-factory-get-started.md).

<!---HONumber=August15_HO8-->