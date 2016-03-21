<properties 
	pageTitle="Déplacer des données vers/depuis DocumentDB | Microsoft Azure" 
	description="Découvrez comment déplacer des données depuis et vers la collection Azure DocumentDB à l’aide d’Azure Data Factory." 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="spelluru"/>

# Déplacer des données vers et depuis DocumentDB à l’aide d’Azure Data Factory

Cet article décrit comment vous pouvez utiliser l'activité de copie dans Azure Data Factory pour déplacer des données vers DocumentDB Azure à partir d'un magasin de données et vice versa. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Les exemples suivants indiquent comment copier des données vers et depuis Azure DocumentDB et Azure Blob Storage. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie d’Azure Data Factory.


## Exemple : copie de données à partir de DocumentDB vers un objet Blob Azure

L’exemple ci-dessous présente les éléments suivants :

1. Un service lié de type [DocumentDb](#azure-documentdb-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'exemple copie des données dans DocumentDB Azure vers un objet Blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié DocumentDB Azure :**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Service lié Azure Blob Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données d'entrée Document DB Azure :**

L’exemple suppose que vous avez une collection nommée **Person** dans une base de données Azure DocumentDB.
 
La définition de « external » : « true » et la spécification de la stratégie externalData informent le service Azure Data Factory qu'il s'agit d'une table qui est externe à la Data Factory et non produite par une activité dans la Data Factory.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Jeu de données de sortie d'objet Blob Azure :**

Les données sont copiées vers un nouvel objet blob toutes les heures. Le chemin d'accès à l'objet blob reflète la valeur date-heure spécifique avec une granularité horaire.

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

Exemple de document JSON dans la collection Person dans une base de données DocumentDB :

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB prend en charge l'interrogation de documents à l'aide d'une syntaxe similaire à SQL sur des documents JSON hiérarchiques.

Exemple : SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

Le pipeline suivant copie les données depuis la collection Person dans la base de données DocumentDB vers un objet blob Azure. Dans le cadre de l'activité de copie, les jeux de données d'entrée et de sortie ont été spécifiés.
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## Exemple : copie de données depuis un objet Blob Azure vers DocumentDB Azure

L’exemple ci-dessous présente les éléments suivants :

1. Un service lié de type [DocumentDb](#azure-documentdb-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


L'exemple copie des données depuis un objet Blob Azure vers DocumentDB Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure Blob Storage :**
	
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Service lié DocumentDB Azure :**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Jeu de données d'entrée d'objet Blob Azure :**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
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
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Jeu de données de sortie DocumentDB Azure :**

L'exemple copie des données vers une collection nommée « Person ».

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

Le pipeline suivant copie les données depuis un objet Blob Azure vers la collection Person dans DocumentDB. Dans le cadre de l'activité de copie, les jeux de données d'entrée et de sortie ont été spécifiés.
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
Si l'entré de l'objet blob d'exemple est

	1,John,,Doe

Alors, la sortie JSON dans DocumentDB sera :

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB est une banque NoSQL pour les documents JSON, où les structures imbriquées sont autorisées. Azure Data Factory permet à l’utilisateur de désigner la hiérarchie via **nestingSeparator**, qui est « . » dans cet exemple. Avec le séparateur, l'activité de copie générera l'objet « Name » avec trois éléments enfants First, Middle et Last, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de la table.

## Propriétés du service lié DocumentDB Azure

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié DocumentDB Azure.

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | --------- |
| type | La propriété de type doit être définie sur : **DocumentDb** | Oui |
| connectionString | Spécifier les informations requises pour se connecter à la base de données DocumentDB Azure. | Oui |

## Propriétés de type du jeu de données DocumentDB Azure

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet blob Azure, table Azure, etc...).
 
La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **DocumentDbCollection** a les propriétés suivantes.

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | -------- |
| collectionName | Nom de la collection de documents DocumentDB. | Oui |


Exemple :

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

### Schéma par Data Factory
Pour les magasins de données sans schéma comme DocumentDB, le service Data Factory déduit le schéma de l’une des manières suivantes :

1.	Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory respecte cette structure en tant que schéma. Dans ce cas, si une ligne ne contient pas de valeur pour une colonne, une valeur null est fournie pour celle-ci.
2.	Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes ne seront pas incluses dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure pratique consiste à définir la structure des données à l’aide de la propriété **structure**.

## Propriétés de type de l'activité de copie DocumentDB Azure

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.
 
**Remarque :** l’activité de copie accepte uniquement une entrée et produit une seule sortie.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d’une activité de copie, quand la source est de type **DocumentDbCollectionSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Spécifier la requête pour lire les données. | Chaîne de requête prise en charge par DocumentDB. <br/>Exemple : SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"<br/> | Non <br/><br/>Si non spécifié, l’instruction SQL exécutée : select <columns defined in structure> from mycollection 
| nestingSeparator | Caractère spécial pour indiquer que le document est imbriqué. | Tout caractère. <br/><br/>DocumentDB est une banque NoSQL pour les documents JSON, où les structures imbriquées sont autorisées. Azure Data Factory permet à l'utilisateur de désigner la hiérarchie via nestingSeparator, qui est « . » dans les exemples ci-dessus. Avec le séparateur, l'activité de copie générera l'objet « Name » avec trois éléments enfants First, Middle et Last, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de la table. | Non

**DocumentDbCollectionSink** prend en charge les propriétés suivantes :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Caractère spécial dans le nom de colonne source pour indiquer que le document imbriqué est nécessaire. <br/><br/>Par exemple, ci-dessus : Name.First dans la table de sortie produit la structure JSON suivante dans le document DocumentDB :<br/><br/>"Name": {<br/> "First": "John"<br/>}, | Caractère utilisé pour séparer les niveaux d’imbrication.<br/><br/>La valeur par défaut est . (point). | Caractère utilisé pour séparer les niveaux d'imbrication. <br/><br/>La valeur par défaut est . (point). | Non | 
| writeBatchSize | Nombre de requêtes parallèles au service DocumentDB pour créer des documents.<br/><br/>Vous pouvez optimiser les performances pendant la copie des données depuis/vers DocumentDB à l’aide de cette propriété. Vous pouvez vous attendre à de meilleures performances lorsque vous augmentez writeBatchSize car davantage de requêtes parallèles à DocumentDB sont envoyées. Toutefois, vous devez éviter les limitations qui peuvent lever le message d’erreur : « Le taux de requête est volumineux ».<br/><br/>La limitation est décidée par un certain nombre de facteurs, y compris la taille des documents, le nombre de termes dans les documents, la stratégie d’indexation de la collection cible, etc. Pour les opérations de copie, vous pouvez utiliser une meilleure collection (par exemple, S3) pour que le débit disponible soit maximal (2 500 unités de requêtes par seconde). | Valeur entière | Non |
| writeBatchTimeout | Temps d'attente pour que l'opération soit terminée avant d'expirer. | (Unité = intervalle de temps) Exemple : « 00:30:00 » (30 minutes). | Non |
 
## Annexe
1. **Question :** l’activité de copie prend-elle en charge la mise à jour d’enregistrements existants ?

	**Réponse :** non.

2. **Question :** comment une nouvelle tentative de copie vers DocumentDB gère-t-elle les enregistrements déjà copiés ?

	**Réponse :** si les enregistrements ont un champ « ID » et que l'opération de copie tente d'insérer un enregistrement avec le même ID, l'opération de copie génère une erreur.
 
3. **Question :** Data Factory prend-il en charge le [partitionnement de données basé sur un intervalle ou sur le hachage](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/) ?

	**Réponse :** non. 
4. **Question :** puis-je indiquer plusieurs collections DocumentDB pour une table ?
	
	**Réponse :** non. Il n’est possible d’indiquer qu’une collection pour le moment.
     

<!------HONumber=AcomDC_0309_2016-->