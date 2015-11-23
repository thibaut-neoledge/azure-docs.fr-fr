<properties 
	pageTitle="Services liés de calcul | Microsoft Azure" 
	description="En savoir plus sur environnements de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory pour transformer/traiter les données." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Services liés de calcul

Cet article décrit les différents environnements de calcul que vous pouvez utiliser pour traiter ou transformer des données. Il fournit également des détails sur les différentes configurations (à la demande ou de type « apporter votre propre configuration ») prises en charge par Data Factory lors de la configuration des services liés qui relient ces environnements de calcul à Azure Data Factory.

## Environnement de calcul à la demande

Dans ce type de configuration, l'environnement de calcul est entièrement géré par le service Azure Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Vous pouvez créer un service lié pour un environnement de calcul à la demande, le configurer et contrôler les paramètres granulaires pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage.

> [AZURE.NOTE]La configuration à la demande est actuellement prise en charge uniquement pour les clusters Azure HDInsight.

## Service lié à la demande Azure HDInsight

Le cluster HDInsight à la demande est automatiquement créé par le service Azure Data Factory pour traiter les données. Le cluster est créé dans la région identique à celle du compte de stockage (propriété linkedServiceName dans JSON) associé au cluster.

Notez les points **importants** suivants sur le service lié HDInsight à la demande :

- Vous ne verrez pas le cluster HDInsight à la demande créé dans votre abonnement Azure ; le service Azure Data Factory gère le cluster HDInsight à la demande pour vous.
- Les journaux des tâches exécutées sur un cluster HDInsight à la demande sont copiés dans le compte de stockage associé au cluster HDInsight. Vous pouvez accéder à ces journaux à partir du portail Azure dans le panneau **Détails sur l'exécution d'activité**. Pour plus de détails, consultez l'article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md). 
- Vous sera facturé uniquement lorsque le cluster HDInsight est actif et exécute des tâches.

> [AZURE.IMPORTANT]Il faut généralement plus de **15 minutes** pour mettre en service un cluster Azure HDInsight à la demande.

### Exemple

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
		  "osType": "linux",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### Propriétés

Propriété | Description | Requis
-------- | ----------- | --------
type | La propriété de type doit être définie sur **HDInsightOnDemand**. | Oui
clusterSize | La taille du cluster à la demande. Spécifiez le nombre de nœuds souhaité dans ce cluster à la demande. | Oui 
jobscontainer | Le conteneur d'objets blob qui contient les données utilisées par les tâches pig/hive/package et où les journaux de cluster seront stockés. | Oui
timetolive | <p>La durée d'inactivité autorisée pour le cluster HDInsight à la demande. Spécifie la durée pendant laquelle le cluster HDInsight à la demande restera actif après l'achèvement d'une activité à exécuter s'il n'existe aucune autre tâche active dans le cluster.</p><p>Par exemple, si une exécution d'activité prend 6 minutes et la propriété timetolive est définie sur 5 minutes, le cluster reste actif pendant 5 minutes après les 6 minutes de traitement d'exécution de l'activité. Si une autre exécution d'activité est exécutée au cours des 6 minutes, elle est traitée par le même cluster.</p><p>La création d'un cluster HDInsight à la demande est une opération coûteuse (peut prendre du temps), donc utilisez ce paramètre pour améliorer les performances d'une fabrique de données en réutilisant un cluster HDInsight à la demande.</p><p>Si vous définissez la valeur de la propriété timetolive sur 0, le cluster est supprimé dès que l'exécution de l'activité est traitée. En revanche, si vous définissez une valeur élevée, le cluster peut rester inactif inutilement entraînant des coûts élevés. Par conséquent, il est important de définir la valeur appropriée en fonction de vos besoins.</p><p>Plusieurs pipelines peuvent partager la même instance du cluster HDInsight à la demande si la valeur de la propriété timetolive est configurée correctement</p> | Oui
version | Version du cluster HDInsight | Non
linkedServiceName | Le magasin d'objets blob utilisé par le cluster à la demande pour le stockage et le traitement des données. | Oui
additionalLinkedServiceNames | Spécifie les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les enregistrer en votre nom. | Non
osType | Type de système d’exploitation. Les valeurs autorisées sont windows (par défaut) et linux | Non 

### Propriétés avancées

Vous pouvez également spécifier les propriétés suivantes pour la configuration granulaire du cluster HDInsight à la demande.

Propriété | Description | Requis
-------- | ----------- | --------
coreConfiguration | Spécifie les paramètres de configuration de base (par exemple, core-site.xml) pour le cluster HDInsight à créer. | Non
hBaseConfiguration | Spécifie les paramètres de configuration HBase (hbase-site.xml) pour le cluster HDInsight. | Non
hdfsConfiguration | Spécifie les paramètres de configuration HDFS (hdfs-site.xml) pour le cluster HDInsight. | Non
hiveConfiguration | Spécifie les paramètres de configuration Hive (hive-site.xml) pour le cluster HDInsight. | Non
mapReduceConfiguration | Spécifie les paramètres de configuration MapReduce (mapred-site.xml) pour le cluster HDInsight. | Non
oozieConfiguration | Spécifie les paramètres de configuration Oozie (oozie-site.xml) pour le cluster HDInsight. | Non
stormConfiguration | Spécifie les paramètres de configuration Storm (storm-site.xml) pour le cluster HDInsight. | Non
yarnConfiguration | Spécifie les paramètres de configuration Yarn (yarn-site.xml) pour le cluster HDInsight. | Non

#### Exemple : configuration de cluster HDInsight à la demande avec les propriétés avancées

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

## Apportez votre propre environnement de calcul 

Dans ce type de configuration, les utilisateurs peuvent inscrire un environnement de calcul existant en tant que service lié dans Data Factory. L'environnement de calcul est géré par l'utilisateur et le service Data Factory l'utilise pour exécuter les activités.
 
Ce type de configuration est pris en charge pour les environnements de calcul suivants :

- Azure HDInsight
- Azure Batch 
- Azure Machine Learning.

## Service lié Azure HDInsight

Vous pouvez créer un service lié Azure HDInsight pour inscrire votre propre cluster HDInsight avec Data Factory.

### Exemple

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### Propriétés

Propriété | Description | Requis
-------- | ----------- | --------
type | La propriété de type doit être définie sur **HDInsight**. | Oui
clusterUri | L'URI du cluster HDInsight. | Oui
username | Spécifiez le nom de l'utilisateur à utiliser pour se connecter à un cluster HDInsight existant. | Oui
password | Spécifiez le mot de passe du compte d'utilisateur. | Oui
location | Spécifiez l'emplacement du cluster HDInsight (par exemple : Ouest des États-Unis). | Oui
linkedServiceName | Nom du service lié pour le stockage d'objets blob utilisé par ce cluster HDInsight. | Oui

## Service lié Azure Batch

Vous pouvez créer un service lié Azure Batch pour inscrire un pool de machines virtuelles (VM) Batch à une fabrique de données. Vous pouvez exécuter des activités personnalisées .NET à l'aide d'Azure Batch ou Azure HDInsight.

Consultez les rubriques suivantes si vous ne connaissez pas le service Azure Batch :
 

- [Présentation technique d'Azure Batch](../batch/batch-technical-overview.md) pour une vue d'ensemble du service Azure Batch.
- Applet de commande [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) pour créer un compte Azure Batch (ou) [portail de gestion Azure](../batch/batch-technical-overview.md) pour créer le compte Azure Batch à l'aide du portail de gestion Azure. Pour obtenir des instructions détaillées sur l'utilisation de l'applet de commande, consultez la rubrique [Utilisation de PowerShell pour gérer un compte Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
- Applet de commande [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) pour créer un pool Azure Batch.

### Exemple

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

Ajoutez « **.<nom région** » au nom de votre compte Batch pour la propriété **accountName**. Exemple :
	
			"accountName": "mybatchaccount.eastus" 

Une autre option consiste à fournir le point de terminaison batchUri comme indiqué ci-dessous.

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

### Propriétés

Propriété | Description | Requis
-------- | ----------- | --------
type | La propriété de type doit être définie sur **AzureBatch**. | Oui
accountName | Nom du compte Azure Batch. | Oui
accessKey | Clé d'accès du compte Azure Batch. | Oui
poolName | Nom du pool de machines virtuelles. | Oui
linkedServiceName | Nom du service lié Azure Storage associé à ce service lié Azure Batch. Ce service lié est utilisé pour présenter les fichiers nécessaires à l'exécution de l'activité et stocker les journaux d'exécution de l'activité. | Oui


## Service lié Microsoft Azure Machine Learning

Vous créez un service lié Azure Machine Learning pour inscrire un point de terminaison de notation par lot Machine Learning pour une fabrique de données.

### Exemple

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### Propriétés

Propriété | Description | Requis
-------- | ----------- | --------
Type | La propriété de type doit être définie sur **AzureML**. | Oui
mlEndpoint | L'URL de la notation par lot. | Oui
apiKey | L'API du modèle d'espace de travail publié. | Oui


## Service lié Analytique Azure Data Lake
Vous créez un service lié **Azure Data Lake Analytics** pour lier un service de calcul Azure Data Lake Analytics à une fabrique de données Azure avant d'utiliser [l'activité U-SQL Analytique Data Lake](data-factory-usql-activity.md) dans un pipeline.

L’exemple suivant présente la définition JSON pour le service lié Analytique Azure Data Lake.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


Le tableau suivant décrit les propriétés utilisées dans la définition JSON.

Propriété | Description | Requis
-------- | ----------- | --------
Type | La propriété de type doit être définie sur **AzureDataLakeAnalytics**. | Oui
accountName | Nom du compte du service Analytique Azure Data Lake. | Oui
dataLakeAnalyticsUri | URI du service Analytique Azure Data Lake. | Non 
autorisation | Le code d'autorisation est automatiquement récupéré après un clic sur le bouton **Autoriser** dans Data Factory Editor et une fois la connexion OAuth effectuée. | Oui 
subscriptionId | ID d'abonnement Azure | Non (si non spécifié, l’abonnement de la fabrique de données est utilisé). 
nom\_groupe\_ressources | Nom du groupe de ressources Azure | Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé).
sessionId | ID de session issu de la session d'autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Il est généré automatiquement dans l’éditeur de la fabrique de données. | Oui


## Service lié Azure SQL

Vous créez un service lié Azure SQL et vous l’utilisez avec l’[activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, voir l’article [Connecteur SQL Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).


  



     
 
   

<!---HONumber=Nov15_HO3-->