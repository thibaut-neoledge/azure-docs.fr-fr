<properties 
	pageTitle="Data Factory - Notes de publication | Microsoft Azure" 
	description="Data Factory release notes" 
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

# Notes de publication sur Azure Data Factory

## Notes relatives à la version du 17/07/2015 de Data Factory
Les modifications JSON suivantes sont introduites dans la version de juillet 2015 d’Azure PowerShell.

## Mettre à jour les types de service lié, de table et d’activité
Type de ressource | Nom actuel dans JSON | Nouveau nom dans JSON
------------- | -------------------- | ----------------
Service lié (Source de données) | AzureSqlLinkedService | AzureSqlDatabase
Service lié (Source de données) | AzureStorageLinkedService | AzureStorage
Service lié (Source de données) | DocumentDbLinkedService | DocumentDb
Service lié (Source de données) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
Service lié (Source de données) | OnPremisesOracleLinkedService | OnPremisesOracle
Service lié (Source de données) | OnPremisesSqlLinkedService | OnPremisesSqlServer
Service lié (Source de données) | OnPremisesMySqlLinkedService | OnPremisesMySql
Service lié (Source de données) | OnPremisesDb2LinkedService | OnPremisesDb2
Service lié (Source de données) | OnPremisesTeradataLinkedService | OnPremisesTeradata
Service lié (Source de données) | OnPremisesSybaseLinkedService | OnPremisesSybase
Service lié (Source de données) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
Service lié (Compute) | AzureMlLinkedService | AzureML
Service lié (Compute) | HDInsightBYOCLinkedService | HDInsight
Service lié (Compute) | HDInsightOnDemandLinkedService | HDInsightOnDemand
Service lié (Compute) | AzureBatchLinkedService | AzureBatch
Dataset | AzureBlobLocation | AzureBlob
Dataset | AzureTableLocation | AzureTable
Dataset | AzureSqlTableLocation | AzureSqlTable
Dataset | DocumentDbCollectionLocation | DocumentDbCollection 
Dataset | OnPremisesFileSystemLocation | FileShare
Dataset | OnPremisesOracleTableLocation | OracleTable
Dataset | OnPremisesSqlServerTableLocation | SqlServerTable
Dataset | RelationTableLocation | RelationalTable
Activité | CopyActivity | Copier
Activité | HDInsightActivity (Transformation Hive) | HDInsightHive
Activité | HDInsightActivity (Transformation Pig) | HDInsightPig
Activité | HDInsightActivity (Transformation MapReduce) | HDInsightMapReduce
Activité | HDInsightActivity (Streaming) | HDInsightHadoopStreaming
Activité | AzureMLBatchScoringActivity | AzureMLBatchScoring
Activité | StoredProcedureActivity | SqlServerStoredProcedure

## Nouvel élément typeProperties
Le nouvel élément **typeProperties** contient des propriétés propres au type pour un service lié/une table/une activité.

### Ancien service lié JSON
	{
	    "name": "StorageLinkedService",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

### Nouveau service lié JSON
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

Notez les points suivants :

- La propriété **type** est déplacée d’un niveau vers le haut et définie sur **AzureStorage** (remplacement d’**AzureStorageLinkedService** par **AzureStorage**). 
- Le nouvel élément **typeProperties** contient des propriétés prises en charge par le service lié Azure Storage (**connectionString** dans cet exemple).  

### Ancien jeu de données JSON
	{
	    "name": "EmpTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureTableLocation",
	            "tableName": "myazuretable",
	            "linkedServiceName": "MyLinkedService"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

### Nouveau jeu de données JSON

	{
	    "name": "EmpTable",
	    "properties": {
	        "type": "AzureTable",
	        "linkedServiceName": "MyLinkedServiceName",
	        "typeProperties": {
	            "tableName": "myazuretable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Notez les points suivants :

- La propriété **type** est déplacée d’un niveau vers le haut et le nom de type **AzureTableLocation** est remplacé par **AzureTable**.
- L’élément **linkedServiceName** est déplacé d’un niveau vers le haut. 
- L’élément **location** est désormais supprimé, et les propriétés propres au type comme **tableName** qui étaient spécifiées dans la section **location** le sont dans la nouvelle section **typeProperties**.  

### Ancienne activité JSON

	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "transformation":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}   

### Nouvelle activité JSON
	
	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}

Notez les points suivants :

- Notez que l’élément **transformation** a été remplacé par le nouvel élément **typeProperties**.

## L’élément waitOnExternal est supprimé.
L’élément **waitOnExternal** est remplacé par les nouvelles propriétés **external** et **externalData**.

### Ancien JSON
	{
	    "name": "EmpTableFromBlob",
	    "properties":
	    {
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
                "waitOnExternal": 
				{
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": "3"			
				}
	        }
	    }
	} 

### Nouveau JSON
	{
	  "name": "EmpTableFromBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": "3"
	      }
	    }
	  }
	}

Notez les points suivants :

- La propriété **waitOnExternal** est supprimée de la section **availability**. 
- Une nouvelle propriété **external** est ajoutée à un niveau supérieur et définie sur la valeur **true** pour une table externe. 
- Les propriétés de l’élément **waitOnExternal** telles que **retryInterval** sont ajoutées dans la nouvelle section **externalData** de l’élément **Policy**.
- L’élément **externalData** est facultatif. 
- Si vous utilisez l’élément **externalData**, vous devez avoir défini la propriété **external** sur **true**. 
 

## Nouvelle propriété copyBehavior pour la classe BlobSink
La classe **BlobSink** prend en charge la nouvelle propriété **copyBehavior**. Cette propriété définit le comportement de copie lorsque la source est **BlobSource** ou **FileSystem**. La propriété **copyBehavior** peut avoir trois valeurs possibles.

**PreserveHierarchy** : conserve la hiérarchie des fichiers dans le dossier cible. Autrement dit, le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.


**FlattenHierarchy** : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Le nom des fichiers cibles est généré automatiquement.


**MergeFiles** : fusionne tous les fichiers du dossier source dans un même fichier. Si le nom de fichier/d’objet blob est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement.
 
## Nouvelle propriété getDebugInfo pour toutes les activités HDInsight
Les activités HDInsight (Hive, Pig, MapReduce, Hadoop Streaming) prennent en charge la nouvelle propriété **getDebugInfo**. La propriété **getDebugInfo** est un élément facultatif. Si elle est définie sur **Failure**, les journaux ne sont téléchargés qu’en cas d’échec de l’exécution. Si elle est définie sur **All**, les journaux sont toujours téléchargés, quel que soit l’état de l’exécution. Si elle est définie sur **None**, aucun journal n’est téléchargé.

  
     

## Notes pour la version du 10/04/2015 de Data Factory
Vous voyez maintenant les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** dans le panneau **TABLE**. Ces listes sont triées selon l’heure de mise à jour de la tranche. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.

-  Vous mettez à jour l’état de la tranche manuellement, par exemple, à l’aide de **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).

Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches. Cliquez sur **Filtre** dans la barre d’outils pour filtrer les tranches.
 
Vous pouvez toujours afficher les tranches triées en fonction de leur heure de tranche en cliquant sur la vignette **Tranches de données (par heure de tranche)**. Les tranches de ces collections sont classées par heure de tranche. Par exemple, dans le cas d’une planification horaire, les tranches sont : - 4/4/2015 17:00 en cours - 4/4/2015 16:00 réussite - 4/4/2015 15:00 échec

Mais, si une tranche antérieure est réexécutée, elle n’apparaît pas en haut de cette liste, même si c’est probablement ce que l’utilisateur souhaite le plus.

## Notes pour la version du 31/03/2015 de Data Factory
- Une mise à jour du package d’installation **Passerelle de gestion des données** a été publiée dans le [Centre de téléchargement Microsoft][adf-gateway-download].
- La copie à partir du **système de fichiers local vers l’objet blob Azure** est maintenant prise en charge. Pour plus d’informations, consultez les rubriques suivantes.
	-  [Service lié de système de fichiers local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propriétés OnPremisesFileSystemLocation dans une table JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) Consultez la matrice de la copie mise à jour et les propriétés **FileSystemSource**. 
-  La copie à partir de la **base de données Oracle locale vers l’objet blob Azure** est maintenant prise en charge. Pour plus d’informations, consultez les rubriques suivantes. 
	-  [Service Oracle lié local (en anglais)](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propriétés OnPremisesOracleTableLocation dans une table JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) Consultez la matrice de copie mise à jour et les propriétés **OracleSource**.
-  Vous pouvez spécifier le codage pour les fichiers texte dans un objet Blob Azure. Consultez la nouvelle [propriété encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Vous pouvez appeler une procédure stockée avec des paramètres supplémentaires lors de la copie dans un récepteur SQL.    

Consultez le billet de blog : [Mise à jour d’Azure Data Factory - nouveaux magasins de données](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) pour plus d’informations, notamment des exemples.

## Notes pour la version du 27/02/2015 de Data Factory

### Nouvelles améliorations
- **Azure Data Factory Editor** Azure Data Factory Editor, qui fait partie du portail Azure en version préliminaire, vous permet de créer, de modifier et de déployer des fichiers JSON qui définissent des services liés, des jeux de données et des pipelines. L'objectif principal de l'éditeur est de vous fournir une interface utilisateur (IU) rapide et facile d'utilisation pour créer des artefacts Azure Data Factory sans avoir à installer Azure PowerShell et accélérer l'utilisation des applets de commande PowerShell. Consultez le billet de blog [Azure Data Factory Editor - Un éditeur web compact][adf-editor-blog] pour accéder à une présentation rapide et à une vidéo sur Data Factory Editor. Pour obtenir une présentation détaillée de l’éditeur, consultez l’article [Data Factory Editor][adf-editor].          

### Changements

## Notes pour la version du 26/1/2015 de Data Factory ##

### Changements
- Une mise à jour du package d’installation **Passerelle de gestion des données** a été publiée dans le [Centre de téléchargement Microsoft][adf-gateway-download]. À partir de cette version, vous pouvez trouver la dernière passerelle de gestion des données à utiliser avec Azure Data Factory à cet emplacement de téléchargement. Ce package d’installation concerne à la fois Azure Data Factory et Power BI pour les services Office 365. Si vous utilisez les deux services, notez que les passerelles pour Data Factory et Power BI doivent être installées sur des ordinateurs différents, et configurées différemment en fonction des instructions figurant dans la documentation de Data Factory ou Power BI.
- L’**activité de copie** prend désormais en charge la copie de données entre la base de données SQL Server locale et une base de données SQL Azure. 
- **SqlSink** prend en charge une nouvelle propriété : **WriteBatchTimeout**. Cette propriété vous permet de configurer la durée d’attente de l’opération d’insertion par lots avant l’arrivée à expiration de l’opération. Pour une copie hybride (opération de copie qui implique une source de données locale et une source de données du cloud), vous devez disposer de la passerelle version 1.4 ou ultérieure pour pouvoir utiliser cette propriété. 
- Le **service lié SQL Server** prend désormais en charge **l’authentification Windows**. 
	- Lors de la création d’un service lié SQL Server à l’aide du portail, vous pouvez maintenant choisir d’utiliser l’authentification Windows et définir les informations d’identification appropriées. Pour cela, vous avez besoin de la passerelle version 1.4 ou ultérieure. 
	- Lors de la création d’un service lié SQL Server à l’aide d’Azure PowerShell, vous pouvez spécifier les informations de connexion en texte brut ou les chiffrer en utilisant l’[applet de commande New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] mise à jour, puis utiliser la chaîne chiffrée pour la propriété de chaîne de connexion dans la charge utile JSON du service lié. Consultez [Services liés][adf-msdn-linked-services] pour plus d’informations sur la définition d’un service lié au format JSON. La fonctionnalité de chiffrement n’est pas encore prise en charge par l’applet de commande New-AzureDataFactoryEncryptValue. 

## Notes pour la version du 11/12/2014 de Data Factory ##

### Nouvelles améliorations

- Intégration d'Azure Machine Learning
	- Cette version du service Azure Data Factory vous permet d’intégrer Azure Data Factory à Azure Machine Learning (ML) via **AzureMLLinkedService** et **AzureMLBatchScoringActivity**. Pour plus d’informations, consultez la page [Créer des pipelines prédictifs à l’aide d’Azure Data Factory et Azure Machine Learning][adf-azure-ml]. 
- Indication de l'état de la version de la passerelle
	- L'état « NewVersionAvailable » est affiché dans le portail Azure en version préliminaire et dans la sortie de l'applet de commande Get-AzureDataFactoryGateway, s'il existe une version de la passerelle plus récente que celle qui est actuellement installée. Vous pouvez ensuite suivre les indications du portail pour télécharger le nouveau fichier d'installation (.msi), et l'exécuter pour installer la passerelle la plus récente. Aucune configuration supplémentaire n'est nécessaire.

### Changements

- Suppression de JobsContainer dans HdInsightOnDemandLinkedService.
	- Dans la définition JSON de HDInsightOnDemandLinkedService, vous n’avez plus besoin de spécifier la propriété **jobsContainer**. Si la propriété est spécifiée pour un service lié à la demande, elle est ignorée. Vous pouvez supprimer la propriété de la définition JSON du service lié, et mettre à jour la définition du service lié à l'aide de l'applet de commande New-AzureDataFactoryLinkedService.
- Paramètres de configuration facultatifs pour HDInsightOnDemandLinkedService
	- Cette version introduit la prise en charge de quelques paramètres de configuration facultatifs pour HDInsightOnDemandLinked (cluster HDInsight à la demande). Pour plus d’informations, consultez les [propriétés de ClusterCreateParameters][on-demand-hdi-parameters].
- Suppression de l'emplacement de la passerelle
	- Durant la création d'une passerelle Azure Data Factory via le portail ou PowerShell (New-AzureDataFactoryGateway), vous n'avez plus besoin de spécifier l'emplacement de la passerelle. La région de la fabrique de données est héritée. De même, pour configurer un service SQL Server lié à l'aide de JSON, la propriété « gatewayLocation » n'est plus nécessaire. Le Kit de développement logiciel (SDK) Data Factory .NET est également mis à jour pour refléter ces changements.
	- Si vous utilisez une ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous devez toujours fournir le paramètre d'emplacement.
 
     

#### Dernières modifications
	
- CustomActivity devient DotNetActivity
	- L’interface **ICustomActivity** est renommée en **IDotNetActivity**. Vous devrez mettre à jour les packages NuGet Data Factory et remplacer ICustomActivity par IDotNetActivity dans le code source de votre activité personnalisée.  
	- Vous devez changer le type d’activité personnalisée dans la définition JSON de votre activité personnalisée en remplaçant **CustomActivity** par **DotNetActivity**. 
	- Les classes **CustomActivity** et **CustomActivityProperties** ont été renommées en **DotNetActivity** et **DotNetActivityProperties** avec le même jeu de propriétés.

		Si vous utilisez l'ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous pouvez continuer à utiliser CustomActivity au lieu de DotNetActivity.
    
  		Consultez [Utilisation des activités personnalisées dans un pipeline Azure Data Factory][adf-custom-activities] pour suivre une procédure pas à pas permettant de créer une activité personnalisée et de l’utiliser dans un pipeline Azure Data Factory.

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-editor]: data-factory-editor.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=August15_HO7-->