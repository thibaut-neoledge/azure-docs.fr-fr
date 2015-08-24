## Mappage de colonnes avec règles de conversion
Le mappage de colonnes peut être utilisé pour spécifier la façon dont les colonnes spécifiées dans la « structure » de la table source sont mappées vers les colonnes spécifiées dans la « structure » de la table du récepteur. La propriété **columnMapping** est disponible dans la section **typeProperties** de l’activité de copie.

Le mappage de colonnes prend en charge les scénarios suivants :

1.	Toutes les colonnes de la « structure » de la table source sont mappées vers toutes les colonnes de la « structure » de la table du récepteur.
2.	Un sous-ensemble des colonnes de la « structure » de la table source sont mappées vers toutes les colonnes de la « structure » de la table du récepteur.

Voici une liste de conditions d’erreur qui entraîneront la levée d’une exception :

1.	La « structure » de la table du récepteur contient un nombre de colonnes inférieur ou supérieur à celui spécifié par le mappage de colonnes.
2.	Mappage en double.
3.	Le résultat de la requête SQL ne comprend pas de nom de colonne qui soit spécifié dans le mappage.

## Exemples de mappages de colonnes
> [AZURE.NOTE]Les exemples ci-dessous concernent SQL Azure et les objets blob Azure, mais sont applicables à tout autre magasin de données prenant en charge les tables de données rectangulaires. Vous devrez ajuster les définitions du jeu de données et du service lié dans les exemples ci-dessous pour pointer vers les données de la source de requise.

### Exemple 1 : mappage de colonnes depuis SQL Azure vers un objet blob Azure
Dans cet exemple, la table d’entrée possède une structure et pointe vers une table SQL comprise dans une base de données SQL Azure.

	{
	    "name": "AzureSQLInput",
	    "properties": {
	        "structure": 
	         [
	           { "name": "userid"},
	           { "name": "name"},
	           { "name": "group"}
	         ],
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
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

Dans cet exemple, la table de sortie possède une structure et pointe vers un objet blob compris dans un stockage d’objets blob Azure.

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "myuserid"},
	                { "name": "myname" },
	                { "name": "mygroup"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Le code JSON de cette activité est fourni ci-dessous. Colonnes de la source mappées vers les colonnes du récepteur (**columnMappings**) en utilisant la propriété **Translator**.

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "AzureSQLInput"  } ],
	    "outputs":  [ { "name": "AzureBlobOutput" } ],
	    "typeProperties":    {
	        "source":
	        {
	            "type": "SqlSource"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
	        }
	    },
	   "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

**Flux du mappage de colonnes** :

![Flux du mappage de colonnes](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### Exemple 2 : mappage de colonnes à l’aide d’une requête SQL depuis SQL Azure vers un objet blob Azure
Dans cet exemple, une requête SQL est utilisée pour extraire des données de SQL Azure au lieu de simplement spécifier le nom de la table et le nom des colonnes dans la section « structure ».

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": " AzureSQLInput"  } ],
	    "outputs":  [ { "name": " AzureBlobOutput" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "Translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
	        }
	    },
	    "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

Dans ce cas, les résultats de la requête sont d’abord mappés vers les colonnes spécifiées dans la « structure » de la source. Ensuite, les colonnes de la « structure » de la source sont mappées vers les colonnes de la « structure » du récepteur avec les règles spécifiées dans columnMappings. Supposons que la requête retourne cinq colonnes, c’est-à-dire deux colonnes de plus que celles spécifiées dans la « structure » de la source.

**Flux du mappage de colonnes**

![Flux du mappage de colonnes 2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)

<!---HONumber=August15_HO7-->