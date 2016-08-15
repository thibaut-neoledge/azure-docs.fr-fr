<properties 
	pageTitle="Déplacer des données depuis Cassandra à l’aide de Data Factory | Microsoft Azure" 
	description="Découvrez comment déplacer des données depuis une base de données Cassandra locale à l’aide d’Azure Data Factory." 
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
	ms.date="07/07/2016" 
	ms.author="spelluru"/>

# Déplacer des données depuis une base de données Cassandra locale à l’aide d’Azure Data Factory
Cet article décrit la façon dont vous pouvez utiliser l’activité de copie dans une Azure Data Factory pour copier des données depuis une base de données Cassandra locale vers n’importe quel magasin de données de la colonne du récepteur dans la section [Sources et récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores). Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasins de données prises en charge.

Actuellement, Data Factory prend uniquement en charge le déplacement de données depuis une base de données Cassandra vers les [magasins récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores), et non le déplacement de données depuis d’autres magasins de données vers une base de données Cassandra.

## Configuration requise
Pour permettre au service Azure Data Factory de se connecter à votre base de données Cassandra locale, vous devez installer ce qui suit :

- Une passerelle de gestion de données version 2.0 ou ultérieure sur l’ordinateur qui héberge la base de données ou sur un autre ordinateur afin d’éviter toute mise en concurrence avec la base de données pour les ressources. La passerelle de gestion de données est un logiciel qui connecte des sources de données locales à des services cloud de manière gérée et sécurisée. Pour plus d’informations sur la passerelle de gestion de données, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md).
  
	L’installation de la passerelle engendre automatiquement l’installation d’un pilote Microsoft ODBC Cassandra, utilisé pour se connecter à la base de données Cassandra.

> [AZURE.NOTE] Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.

## Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données à partir d’une base de données Cassandra vers n’importe quel magasin de données récepteur pris en charge consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline à l’aide de l’Assistant de copie](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Exemple : copie de données à partir de Cassandra vers un objet blob
L’exemple copie des données à partir d’une base de données Cassandra vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples. Les données peuvent être copiées directement vers l’un des récepteurs indiqués dans l’article [Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores), par le biais de l’activité de copie d’Azure Data Factory.

- Un service lié de type [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [CassandraTable](#cassandratable-properties).
- Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [CassandraSource](#cassandrasource-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Service lié Cassandra**

Cet exemple utilise le service lié **Cassandra**. Consultez la section [Service lié Cassandra](#onpremisescassandra-linked-service-properties) pour visualiser les propriétés prises en charge par ce service lié.

	{
    	"name": "CassandraLinkedService",
    	"properties":
    	{
			"type": "OnPremisesCassandra",
			"typeProperties":
			{
				"authenticationType": "Basic",
				"host": "mycassandraserver",
				"port": 9042,
				"username": "user",
				"password": "password",
				"gatewayName": "mygateway"
        	}
    	}
	}


**Service lié Azure Storage**

	{
		"name": "AzureStorageLinkedService",
		"properties": {
		"type": "AzureStorage",
			"typeProperties": {
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
			}
		}
	}

**Jeu de données d’entrée Cassandra**

	{
		"name": "CassandraInput",
		"properties": {
			"linkedServiceName": "CassandraLinkedService",
			"type": "CassandraTable",
			"typeProperties": {
				"tableName": "mytable",
				"keySpace": "mykeyspace" 
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

La définition de **external** sur **true** informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à la Data Factory et non produit par une activité dans la Data Factory.

**Jeu de données de sortie d’objet Blob Azure**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1).

	{
		"name": "AzureBlobOutput",
		"properties":
		{
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties":
			{
				"folderPath": "adfgetstarted/fromcassandra"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1
			}
		}
	}


**Pipeline avec activité de copie**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie ci-dessus, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **CassandraSource** et le type **sink** est défini sur **BlobSink**.

Pour obtenir la liste des propriétés prises en charge par RelationalSource, consultez [propriétés du type RelationalSource](#cassandrasource-type-properties).
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "CassandraToAzureBlob",
				"description": "Copy from Cassandra to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "CassandraInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "CassandraSource",
						"query": "select id, firstname, lastname from mykeyspace.mytable"
		
					},
					"sink": {
						"type": "BlobSink"
					}
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
					"concurrency": 1,
					"executionPriorityOrder": "OldestFirst",
					"retry": 0,
					"timeout": "01:00:00"
				}
			}
			]	
		}
	}
## Propriétés du service lié OnPremisesCassandra

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Cassandra.

| Propriété | Description | Requis |
| -------- | ----------- | -------- | 
| type | Le type de propriété doit être défini sur : **OnPremisesCassandra** | Oui |
| host | Une ou plusieurs adresses IP ou noms d’hôte de serveurs Cassandra.<br/><br/>Renseignez une liste des adresses IP ou des noms d’hôte séparée par des virgules pour vous connecter simultanément à tous les serveurs. | Oui | 
| port | Le port TCP utilisé par le serveur Cassandra pour écouter les connexions clientes. | Non, valeur par défaut : 9042 |
| authenticationType | Basique ou anonyme | Oui |
| username |Spécifiez le nom d’utilisateur du compte d’utilisateur. | Oui, si authenticationType est défini sur De base. |
| password | Spécifiez le mot de passe du compte d'utilisateur. | Oui, si authenticationType est défini sur De base. |
| gatewayName | Le nom de la passerelle qui sera utilisée pour se connecter à la base de données Cassandra locale. | Oui |
| Encryptedcredential | Informations d’identification chiffrées par la passerelle. | Non | 

## Propriétés de CassandraTable

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **CassandraTable** a les propriétés suivantes

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| espace de clé | Nom de l’espace de clé ou du schéma dans la base de données Cassandra. | Oui (si la **requête** pour **CassandraSource** n’est pas définie). |
| TableName | Nom de la table dans la base de données Cassandra. | Oui (si la **requête** pour **CassandraSource** n’est pas définie). |


## Propriétés de type CassandraSource
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés telles que le nom, la description, les tables d'entrée et de sortie, les différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d’une activité de copie, quand la source est de type **CassandraSource**, les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| query | Utilise la requête personnalisée pour lire des données. | Requête SQL-92 ou requête CQL. Reportez-vous à [référence CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Lorsque vous utilisez la requête SQL, indiquez **keyspace name.table name** pour représenter la table que vous souhaitez interroger. | Non (si tableName et keyspace sur le jeu de données sont définis). |
| Niveau de cohérence | Le niveau de cohérence spécifie le nombre de réplicas devant répondre à une demande de lecture avant de renvoyer des données à l’application cliente. Cassandra vérifie le nombre de réplicas spécifié pour permettre aux données de répondre à la demande de lecture. | UN, DEUX, TROIS, QUORUM, TOUT, LOCAL\_QUORUM, EACH\_QUORUM, LOCAL\_ONE. Reportez-vous à [Configuring data consistency (Configuration de la cohérence des données)](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) pour plus d’informations. | Non. La valeur par défaut est UN. |  


### Mappage de type pour Cassandra
Type Cassandra | Type basé sur .Net
--------------- | ---------------
ASCII |	String 
BIGINT | Int64
BLOB | Byte
BOOLEAN | Boolean
DÉCIMAL | Décimal
DOUBLE | Double
FLOAT | Single
INET | String
INT | Int32
TEXTE | String
TIMESTAMP | DateTime
TIMEUUID | Guid
UUID | Guid
VARCHAR | String
VARINT | Décimal

> [AZURE.NOTE]  
Pour les types de collections (mappages, ensembles, listes, etc.), reportez-vous à la section [Work with Cassandra collection types using virtual table (Travailler avec les types de collections Cassandra à l’aide d’une table virtuelle)](#work-with-collections-using-virtual-table).
> 
> Les types définis par l’utilisateur ne sont pas pris en charge.
> 
> La longueur des colonnes binaires et des colonnes de chaîne ne peut pas être supérieure à 4 000.

## Travailler avec des collections à l’aide d’une table virtuelle
Azure Data Factory utilise un pilote ODBC intégré pour assurer la connexion à votre base de données Cassandra et copier des données à partir de cette dernière. Pour les types de collection, notamment les cartes, ensembles et listes, le pilote renormalisera les données dans des tables virtuelles correspondantes. En particulier, si une table contient des colonnes de n’importe quelle collection, le pilote génère les tables virtuelles suivantes :

-	Une **table de base**, qui contient les mêmes données que la table réelle, à l’exception des colonnes de collection. La table de base utilise le même nom que la table réelle qu’elle représente.
-	Une **table virtuelle** pour chaque colonne de collection, qui étend les données imbriquées. Le nom des tables virtuelles qui représentent des collections est composé du nom de la table réelle, du séparateur «_vt_» et du nom de la colonne.

Les tables virtuelles font référence aux données présentées dans la table réelle, de manière à permettre au pilote d’accéder aux données dénormalisées. Consultez la section exemple ci-dessous pour plus d’informations. Vous pouvez accéder au contenu des collections Cassandra en interrogeant et en joignant les tables virtuelles.

Vous pouvez utiliser l’[Assistant de copie](data-factory-data-movement-activities.md#data-factory-copy-wizard) afin d’afficher de manière intuitive la liste des tables dans la base de données Cassandra, y compris les tables virtuelles, et de prévisualiser les données qui s’y trouvent. Vous pouvez également construire une requête dans l’Assistant de copie et valider pour voir le résultat.

### Exemple
Par exemple, « ExampleTable » ci-dessous est une table de base de données Cassandra qui contient une colonne clé primaire entière nommée « pk\_int », une colonne de texte nommée « value », une colonne de liste, une colonne de mappage et une colonne de jeu (nommée « StringSet »).

pk\_int | Valeur | Énumérer | Mappage |	StringSet
------ | ----- | ---- | --- | --------
1 | « exemple de valeur 1 » | [« 1 », « 2 », « 3 »] | {« S1 » : « a », « S2 » : « b »} | {« A », « B », « C »}
3 | « exemple de valeur 3 » | [« 100 », « 101 », « 102 », « 105 »] | {« S1 » : « t »} | {« A », « E »}

Le pilote génère plusieurs tables virtuelles pour représenter cette table. Les colonnes de clés étrangères dans les tables virtuelles font référence aux colonnes de clés primaires dans la table réelle, et indiquent à quelles lignes de la table réelle les lignes de la table virtuelle correspondent.

La première table virtuelle est la table de base ci-dessous nommée « ExampleTable ». La table de base contient les mêmes données que la table de base de données d’origine, à l’exception des collections, qui sont omises de cette table et développées dans d’autres tables virtuelles.

pk\_int | Valeur
------ | -----
1 | « exemple de valeur 1 »
3 | « exemple de valeur 3 »

Les tableaux suivants montrent les tables virtuelles qui renormalisent les données des colonnes Liste, Mappage et StringSet. Les colonnes portant des noms se terminant par « \_index » ou « \_key » indiquent la position des données dans la liste ou le mappage d’origine. Les colonnes portant des noms se terminant par « \_value » contiennent les données étendues de la collection.

#### Table « ExampleTable\_vt\_List » :
pk\_int | List\_index | List\_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### Table « ExampleTable\_vt\_List » :
pk\_int | Map\_key | Map\_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### Table « ExampleTable\_vt\_List » :
pk\_int | StringSet\_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!---HONumber=AcomDC_0803_2016-->