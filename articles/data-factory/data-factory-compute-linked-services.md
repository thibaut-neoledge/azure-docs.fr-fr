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
	ms.date="05/31/2016"
	ms.author="spelluru"/>

# Services liés de calcul

Cet article décrit les différents environnements de calcul que vous pouvez utiliser pour traiter ou transformer des données. Il fournit également des détails sur les différentes configurations (à la demande ou de type « apporter votre propre configuration ») prises en charge par Data Factory lors de la configuration des services liés qui relient ces environnements de calcul à Azure Data Factory.

## Environnement de calcul à la demande

Dans ce type de configuration, l'environnement de calcul est entièrement géré par le service Azure Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Vous pouvez créer un service lié pour un environnement de calcul à la demande, le configurer et contrôler les paramètres granulaires pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage.

> [AZURE.NOTE] La configuration à la demande est actuellement prise en charge uniquement pour les clusters Azure HDInsight.

## Service lié à la demande Azure HDInsight
Le service Azure Data Factory peut automatiquement créer un cluster HDInsight à la demande sous Windows/Linux pour traiter les données. Le cluster est créé dans la région identique à celle du compte de stockage (propriété linkedServiceName dans JSON) associé au cluster.

Notez les points **importants** suivants sur le service lié HDInsight à la demande :

- Vous ne verrez pas le cluster HDInsight à la demande créé dans votre abonnement Azure ; le service Azure Data Factory gère le cluster HDInsight à la demande pour vous.
- Les journaux des tâches exécutées sur un cluster HDInsight à la demande sont copiés dans le compte de stockage associé au cluster HDInsight. Vous pouvez accéder à ces journaux à partir du portail Azure dans le panneau **Détails sur l'exécution d'activité**. Pour plus de détails, consultez l'article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).
- Vous sera facturé uniquement lorsque le cluster HDInsight est actif et exécute des tâches.

> [AZURE.IMPORTANT] Il faut généralement plus de **15 minutes** pour mettre en service un cluster Azure HDInsight à la demande.

### Exemple
Le JSON suivant définit un service lié HDInsight à la demande sous Linux. Le service Data Factory crée automatiquement un cluster HDInsight **sous Linux** lors du traitement d’une tranche de données.


	{
	    "name": "HDInsightOnDemandLinkedService",
	    "properties": {
	        "type": "HDInsightOnDemand",
	        "typeProperties": {
	            "clusterSize": 4,
	            "timeToLive": "00:05:00",
	            "osType": "linux",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

Pour utiliser un cluster HDInsight Windows, définissez **osType** sur **windows** ou n’utilisez pas la propriété si la valeur par défaut est windows.

> [AZURE.IMPORTANT] 
Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. C’est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé dès qu’une tranche doit être traitée, sauf s’il existe un cluster activé (**timeToLive**), puis il est supprimé à la fin du traitement.
> 
> Comme un nombre croissant de tranches sont traitées, vous verrez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs respecte ce modèle : "adf**nomdevotredatafactory**-**linkedservicename**-datetimestamp". Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.

### Propriétés

Propriété | Description | Requis
-------- | ----------- | --------
type | La propriété de type doit être définie sur **HDInsightOnDemand**. | Oui
clusterSize | Nombre de nœuds worker/données dans le cluster. Le cluster HDInsight est créé avec 2 nœuds principaux et le nombre de nœuds worker que vous spécifiez pour cette propriété. Les nœuds étant de taille Standard\_D3 à 4 cœurs, un cluster à 4 nœuds worker prend 24 cœurs (4 x 4 pour les nœuds de travail + 2 x 4 pour les nœuds principaux). Pour plus d’informations sur le niveau Standard\_D3, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Oui
timetolive | La durée d’inactivité autorisée pour le cluster HDInsight à la demande. Spécifie la durée pendant laquelle le cluster HDInsight à la demande reste actif une fois l'exécution d'une activité terminée s'il n'existe aucune autre tâche active dans le cluster.<br/><br/>Par exemple, si l'exécution d'une activité prend 6 minutes alors que la propriété timetolive est définie sur 5 minutes, le cluster reste actif pendant 5 minutes après les 6 minutes de traitement d'exécution de l'activité. Si une autre activité est exécutée au cours de ces 6 minutes, elle est traitée par le même cluster.<br/><br/>La création d'un cluster HDInsight à la demande est une opération coûteuse (pouvant prendre du temps). Utilisez ce paramètre pour améliorer les performances d'une fabrique de données en réutilisant un cluster HDInsight à la demande.<br/><br/>Si vous définissez la valeur de la propriété timetolive sur 0, le cluster est supprimé dès que l'exécution de l'activité est traitée. En revanche, si vous définissez une valeur élevée, le cluster peut rester inactif inutilement entraînant des coûts élevés. Ainsi, il est important de définir la valeur appropriée en fonction de vos besoins.<br/><br/>Plusieurs pipelines peuvent partager la même instance du cluster HDInsight à la demande si la valeur de la propriété timetolive est configurée correctement. | Oui
version | Version du cluster HDInsight. La valeur par défaut est 3.1 pour le cluster Windows et 3.2 pour le cluster Linux. | Non
linkedServiceName | Le magasin d'objets blob utilisé par le cluster à la demande pour le stockage et le traitement des données. | Oui
additionalLinkedServiceNames | Spécifie les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les enregistrer en votre nom. | Non
osType | Type de système d'exploitation. Les valeurs autorisées sont Windows (par défaut) et Linux. | Non
hcatalogLinkedServiceName | Le nom du service lié à SQL Azure pointant vers la base de données HCatalog. Le cluster HDInsight à la demande sera créé à l’aide de la base de données SQL Azure comme metastore. | Non


#### Exemple JSON additionalLinkedServiceNames

    "additionalLinkedServiceNames": [
        "otherLinkedServiceName1",
		"otherLinkedServiceName2"
  	]
 
### Propriétés avancées

Vous pouvez également spécifier les propriétés suivantes pour la configuration granulaire du cluster HDInsight à la demande.

Propriété | Description | Requis
:-------- | :----------- | :--------
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
	      "timeToLive": "01:30:00",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx4000m",
	        "mapreduce.map.java.opts": "-Xmx4000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000",
	        "mapreduce.map.memory.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

### Tailles de nœuds
Vous pouvez spécifier la taille du nœud principal, du nœud de données et du nœud zookeeper en utilisant les propriétés suivantes.

Propriété | Description | Requis
:-------- | :----------- | :--------
headNodeSize | Spécifie la taille du nœud principal. La valeur par défaut est Standard\_D3. Consultez la section **Spécification des tailles de nœud** ci-dessous pour plus d’informations. | Non
dataNodeSize | Spécifie la taille du nœud de données. La valeur par défaut est Standard\_D3. | Non
zookeeperNodeSize | Spécifie la taille du nœud ZooKeeper. La valeur par défaut est Standard\_D3. | Non
 
#### Spécification des tailles de nœud
Veuillez consulter l’article [Tailles de machines virtuelles](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) pour connaître les valeurs de chaîne que vous devez spécifier pour les propriétés ci-dessus. Les valeurs doivent être conformes aux **applets de commande et API** référencées dans l’article. Comme vous pouvez le voir dans l’article, le nœud de données de grande taille (par défaut) possède 7 Go de mémoire, ce qui risque de s’avérer insuffisant pour votre scénario.

Si vous souhaitez créer des nœuds principaux et des nœuds de travail de taille D4, vous devez spécifier la valeur **Standard\_D4** pour les propriétés headNodeSize et dataNodeSize.

	"headNodeSize": "Standard_D4",	
	"dataNodeSize": "Standard_D4",

Si vous spécifiez une valeur incorrecte pour ces propriétés, vous risquez de rencontrer l’**erreur** suivante : Impossible de créer le cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'PreClusterCreationValidationFailure'. Lorsque vous recevez ce message d’erreur, vérifiez que vous utilisez le nom des **applets de commande et API** du tableau fourni dans l’article ci-dessus.



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
linkedServiceName | Nom du service lié pour le stockage d'objets blob utilisé par ce cluster HDInsight. | Oui

## Service lié Azure Batch

Vous pouvez créer un service lié Azure Batch pour inscrire un pool de machines virtuelles (VM) Batch à une fabrique de données. Vous pouvez exécuter des activités personnalisées .NET à l'aide d'Azure Batch ou Azure HDInsight.

Consultez les rubriques suivantes si vous ne connaissez pas le service Azure Batch :


- [Présentation de base d’Azure Batch](../batch/batch-technical-overview.md) pour une vue d’ensemble du service Azure Batch.
- Applet de commande [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) pour créer un compte Azure Batch (ou) [Portail Azure](../batch/batch-account-create-portal.md) pour créer le compte Azure Batch à l’aide du portail Azure. Pour obtenir des instructions détaillées sur l’utilisation de l’applet de commande, consultez la rubrique [Utilisation de PowerShell pour gérer un compte Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
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

Ajoutez « **.<nom région** » au nom de votre compte Batch pour la propriété **accountName**. Exemple :

			"accountName": "mybatchaccount.eastus"

Une autre option consiste à fournir le point de terminaison batchUri comme indiqué ci-dessous.

			"accountName": "adfteam",
			"batchUri": "https://eastus.batch.azure.com",

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
Vous créez un service lié **Analytique Azure Data Lake** pour lier un service de calcul Analytique Azure Data Lake Analytics à une fabrique de données Azure avant d’utiliser l’[activité U-SQL Analytique Data Lake](data-factory-usql-activity.md) dans un pipeline.

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
autorisation | Le code d’autorisation est automatiquement récupéré après un clic sur le bouton **Autoriser** dans l’éditeur de la fabrique de données et une fois la connexion OAuth effectuée. | Oui
subscriptionId | ID d'abonnement Azure | Non (si non spécifié, l’abonnement de la fabrique de données est utilisé).
nom\_groupe\_ressources | Nom du groupe de ressources Azure | Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé).
sessionId | ID de session issu de la session d'autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Il est généré automatiquement dans l’éditeur de la fabrique de données. | Oui

Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** arrive à expiration au bout d’un certain temps. Consultez le tableau suivant pour connaître les délais d’expiration associés aux différents types de comptes d’utilisateur. Vous pouvez rencontrer le message d’erreur suivant lors de l’**expiration du jeton** d’authentification : Erreur de l’opération d’informations d’identification : invalid\_grant - AADSTS70002: Erreur lors de la validation des informations d’identification. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z ».

| Type d’utilisateur | Expire après |
| :-------- | :----------- | 
| Comptes d’utilisateurs NON gérés par Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 heures |
| Comptes d’utilisateurs gérés par Azure Active Directory (AAD) | 14 jours après la dernière exécution de tranche de données. <br/><br/>90 jours, si une tranche basée sur un service lié OAuth est exécutée au moins une fois tous les 14 jours. |
 
Pour éviter ou résoudre cette erreur, vous devez accorder une nouvelle autorisation à l’aide du bouton **Autoriser** au moment où le **jeton expire**, puis redéployer le service lié. Vous pouvez également générer des valeurs pour les propriétés sessionId et authorization à l’aide du code fourni dans la section suivante.

### Pour générer les valeurs des propriétés sessionId et authorization au moyen d’un programme 
Le code suivant génère les valeurs des propriétés **sessionId** et **authorization**.

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consultez les rubriques [AzureDataLakeStoreLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) et [AuthorizationSessionGetResponse, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) pour plus d’informations sur les classes Data Factory utilisées dans le code. Vous devez ajouter une référence à Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog.
 

## Service lié Azure SQL

Créez un service lié Azure SQL et utilisez-le avec l’[activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).

<!---HONumber=AcomDC_0601_2016-->