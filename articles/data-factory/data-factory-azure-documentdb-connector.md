<properties 
	pageTitle="Connecteur DocumentDB - Déplacer des données vers et à partir de DocumentDB" 
	description="En savoir plus sur le connecteur DocumentDB Azure pour le service Data Factory qui vous permet de déplacer des données vers/depuis une collection DocumentDB Azure" 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Connecteur DocumentDB - Déplacer des données vers et à partir de DocumentDB

Cet article décrit comment vous pouvez utiliser l'activité de copie dans Azure Data Factory pour déplacer des données vers DocumentDB Azure à partir d'un magasin de données et vice versa. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasin de données prises en charge.

## Exemple : copie de données à partir de DocumentDB vers un objet Blob Azure

L'exemple ci-dessous présente les éléments suivants :

1. Un service lié de type DocumentDb.
2. Un service lié de type AzureStorage. 
3. Un jeu de données d'entrée de type DocumentDbCollection. 
4. Un jeu de données de sortie de type AzureBlob.
4. Un pipeline avec une activité de copie qui utilise DocumentDbCollectionSource et BlobSink.

L'exemple copie des données dans DocumentDB Azure vers un objet Blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié DocumentDB Azure :**

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

**Jeu de données d'entrée Document DB Azure :**

L'exemple suppose que vous avez une collection nommée **Person** dans une base de données DocumentDB Azure.
 
La définition de « external » : « true » et la spécification de la stratégie externalData informent le service Azure Data Factory qu'il s'agit d'une table qui est externe à la Data Factory et non produite par une activité dans la Data Factory.

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

Exemple de document JSON dans la collection Person dans une base de données DocumentDB :

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB prend en charge l'interrogation de documents à l'aide d'une syntaxe similaire à SQL sur des documents JSON hiérarchiques.

Exemple : SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

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

## Exemple : copie de données depuis un objet Blob Azure vers DocumentDB Azure

L'exemple ci-dessous présente les éléments suivants :

1. Un service lié de type DocumentDb.
2. Un service lié de type AzureStorage.
3. Un jeu de données d'entrée de type AzureBlob.
4. Un jeu de données de sortie de type DocumentDbCollection. 
4. Un pipeline avec une activité de copie qui utilise BlobSource et DocumentDbCollectionSink.

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

**Jeu de données de sortie DocumentDB Azure :**

L'exemple copie des données vers une collection nommée « Person ».

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

Alors, la sortie JSON dans DocumentDB sera :

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB est une banque NoSQL pour les documents JSON, où les structures imbriquées sont autorisées. Azure Data Factory permet à utilisateur de désigner la hiérarchie via **nestingSeparator**, qui est « . » dans cet exemple. Avec le séparateur, l'activité de copie générera l'objet « Name » avec trois éléments enfants First, Middle et Last, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de la table.

## Propriétés du service lié DocumentDB Azure

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié DocumentDB Azure.

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | --------- |
| type | La propriété de type doit être définie sur : **DocumentDb** | Oui |
| connectionString | Spécifier les informations requises pour se connecter à la base de données DocumentDB Azure. | Oui |

## Propriétés de type du jeu de données DocumentDB Azure

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l'article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet blob Azure, table Azure, etc...).
 
La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **DocumentDbCollection** a les propriétés suivantes.

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | -------- |
| collectionName | Nom de la collection de documents DocumentDB. | Oui |


Exemple :

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

## Propriétés de type de l'activité de copie DocumentDB Azure

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.
 
**Remarque :** l'activité de copie accepte uniquement une entrée et produit une seule sortie.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d'une activité de copie, lorsque la source est de type **DocumentDbCollectionSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Spécifier la requête pour lire les données. | Chaîne de requête prise en charge par DocumentDB. <p>Exemple : SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"</p> | Non <p>Si non spécifié, l'instruction SQL exécutée : sélectionnez <columns defined in structure> dans mycollection </p>
| nestingSeparator | Caractère spécial pour indiquer que le document est imbriqué. | Tout caractère. <p>DocumentDB est une banque NoSQL pour les documents JSON, où les structures imbriquées sont autorisées. Azure Data Factory permet à l'utilisateur de désigner la hiérarchie via nestingSeparator, qui est « . » dans les exemples ci-dessus. Avec le séparateur, l'activité de copie générera l'objet « Name » avec trois éléments enfants First, Middle et Last, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de la table.</p> | Non

**DocumentDbCollectionSink** prend en charge les propriétés suivantes :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Caractère spécial dans le nom de colonne source pour indiquer que le document imbriqué est nécessaire. <p>Par exemple, ci-dessus : Name.First dans la table de sortie produit la structure JSON suivante dans le document DocumentDB : </p><p>« Nom » : {<br/>« First » : « John »<br/>},</p> | Caractère utilisé pour séparer les niveaux d'imbrication.<p>La valeur par défaut est . (point).</p> | Caractère utilisé pour séparer les niveaux d'imbrication. <p>La valeur par défaut est . (point).</p> | Non | 
| writeBatchSize | Nombre de requêtes parallèles au service DocumentDB pour créer des documents.<p>Vous pouvez optimiser les performances lors de la copie des données depuis/vers DocumentDB à l'aide de cette propriété. Vous pouvez vous attendre à de meilleures performances lorsque vous augmentez writeBatchSize car davantage de requêtes parallèles à DocumentDB sont envoyées. Toutefois, vous devrez éviter les limitations qui peuvent lever le message d'erreur : « Le taux de requête est volumineux ».</p><p>La limitation est décidée par un certain nombre de facteurs, y compris la taille des documents, le nombre de termes dans les documents, la stratégie d'indexation de la collection cible, etc. Pour les opérations de copie, vous pouvez utiliser une meilleure collection (par exemple, S3) pour que le débit disponible soit maximal (2 500 unités de requêtes par seconde).</p> | Valeur entière | Non |
| writeBatchTimeout | Temps d'attente pour que l'opération soit terminée avant d'expirer. | (Unité = intervalle de temps) Exemple : « 00:30:00 » (30 minutes). | Non |
 
 

<!---HONumber=August15_HO6-->