<properties 
	pageTitle="Scénarios avancés pour l'utilisation de l'activité de copie avec Azure Data Factory" 
	description="Décrit des scénarios avancés pour l'utilisation de l'activité de copie avec Azure Data Factory." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Scénarios avancés pour l'utilisation de l'activité de copie avec Azure Data Factory 
## Vue d'ensemble
Vous pouvez utiliser l'**activité de copie** dans un pipeline pour copier les données d'une source vers un récepteur (destination) au sein d'un lot. Cette rubrique décrit les scénarios avancés pris en charge par l'activité de copie.


## Filtrage de colonne à l'aide de la définition de structure
En fonction du type de table, il est possible de spécifier un sous-ensemble de colonnes à partir de la source en indiquant un nombre de colonnes dans la définition de **Structure** de la définition de table inférieur à ceux qui se trouvent dans la source de données sous-jacente. Le tableau suivant fournit des informations sur la logique de filtrage de colonne pour différents types de table.

| Type de table | Logique de filtrage de colonne |
|-------------------|----------------------- |
| AzureBlobLocation |La définition de Structure dans la table JSON doit correspondre à la structure de l’objet blob. Pour sélectionner un sous-ensemble des colonnes, utilisez la fonctionnalité de mappage de colonnes décrite dans la section suivante, Règles de transformation : mappage de colonnes. | 
| AzureSqlTableLocation et OnPremisesSqlServerTableLocation | Si la propriété SqlReaderQuery est spécifiée dans le cadre de la définition de l’activité de copie, la définition de Structure de la table doit être en harmonie avec les colonnes sélectionnées dans la requête. Si la propriété SqlReaderQuery n’est pas spécifiée, l’activité de copie construit automatiquement une requête SELECT basée sur les colonnes spécifiées dans la définition de Structure de la définition de table. |
| AzureTableLocation | La section Structure de la définition de la table peut contenir un ensemble complet ou un sous-ensemble des colonnes de la table Azure sous-jacente.

## Règles de transformation : mappage de colonnes
Le mappage de colonne peut spécifier la façon dont les colonnes de la table source sont mappées vers les colonnes de table du récepteur. Cette méthode prend en charge les scénarios suivants :

- Mappage de toutes les colonnes de la table source « structure » vers la table de destination « structure ».
- Un sous-ensemble de colonnes de la table source « structure » est mappé vers toutes les tables de destination « structure ».

Les éléments suivants ne sont pas pris en charge, ce qui lève une exception :

- Un nombre de colonnes inférieur ou supérieur dans la destination.
- Mappage en double.
- Le résultat de la requête SQL n'a pas de nom de colonne

Plus précisément, lors d’une copie de données entre deux objets blob Azure, l’activité de copie se comporte principalement comme une copie de données binaires, sauf si les 3 scénarios suivants se présentent :


1. Si les tables d'entrée et de sortie ont un format différent, l'activité de copie effectue une conversion de format.
2. Si la table d'entrée est spécifiée comme un dossier pouvant contenir plusieurs fichiers et qu’une table de sortie est spécifiée en tant que fichier, l’activité de copie fusionne les fichiers sous le dossier source en un seul récepteur unique.
3. Si le paramètre « columnMapping » est spécifié, l’activité de copie effectue la transformation de données correspondante.


### Exemple 1 : mappage de colonne de SQL Server vers un objet blob Azure
Dans cet exemple, la **table d'entrée** est définie comme suit. La table d'entrée a une structure qui pointe vers une table SQL dans une base de données SQL Server.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
				"location":
				{
					"type": "OnPremisesSqlServerTableLocation",
					"tableName": "MyTable",
					"linkedServiceName": "MyOnPremisesSQLDB"
				},
				"availability":	
				{
    				"frequency": "Hour",
    				"interval": 1
				}
     		}
		}

Dans cet exemple, la **table de sortie** est définie comme suit. La table de sortie a une structure qui pointe vers un objet blob dans un stockage d'objets blob Azure.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
    	    	"format":
    	    	{
    	        	"type": "TextFormat",
		            "columnDelimiter": ",",
    		        "rowDelimiter": ";",
    		        "EscapeChar": "$",
    		        "NullValue": "NaN"
    		    }
			},
			"availability":
			{
    			"frequency": "Hour",
    			"interval": 1
			}
		}
	}	

Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété **partitionedBy**. Dans l'exemple suivant, **folderPath** utilise l'année, le mois et le jour à partir de SliceStart (heure de début de la tranche en cours de traitement) et fileName utilise Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### Exemple : définition du mappage de colonnes
Dans cet exemple, l'activité d'un pipeline est définie comme suit. Colonnes de la source mappées sur les colonnes du récepteur (**columnMappings**) en utilisant la propriété **Translator**.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
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
      			"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Mappage de colonne][image-data-factory-column-mapping-1]

### Exemple 2 : mappage de colonne avec une requête SQL à partir de SQL Server vers un objet blob Azure
Dans cet exemple, une requête SQL (par opposition à la table dans l'exemple précédent) est utilisée pour extraire des données à partir d'un serveur SQL Server local et les colonnes provenant des résultats de la requête sont mappés vers un artefact source, puis vers l'artefact de destination. Pour les besoins de cet exemple, la requête renvoie 5 colonnes.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
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
			"translator": 
			{
      			"type": "TabularTranslator",
      			"columnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Mappage de colonne 2][image-data-factory-column-mapping-2]

## Gestion des types de données par l'activité de copie

Les types de données spécifiés dans la section Structure de la définition de table sont uniquement respectés pour **BlobSource**. Le tableau ci-dessous décrit la façon dont les types de données sont gérés pour les autres types de source et de récepteur.

| Source/Récepteur | Logique de gestion du type de données |
| ----------- | ------------------------ |
| SqlSource | Les types de données définis dans la section Structure de la définition de la table sont ignorés. Les types de données définis dans la base de données SQL sous-jacente sont utilisés pour l'extraction de données durant l'activité de copie. |
| SqlSink | Les types de données définis dans la section Structure de la définition de la table sont ignorés. Les types de données de la source et de la destination sous-jacentes sont comparés. Par ailleurs, une conversion de type implicite est effectuée s'il existe des incompatibilités de types. |
| BlobSource | Lors du transfert de BlobSource vers BlobSink, il n’existe aucune transformation de type ; les types de données définis dans la section Structure de la définition de la table sont ignorés. Pour les destinations autres que BlobSink, les types de données définis dans la section Structure de la définition de la table sont respectés. Si la Structure n’est pas spécifiée dans la définition de la table, la gestion du type dépend de la propriété de format de la table BlobSource, TextFormat : tous les types de colonne sont traités en tant que chaîne, et tous les noms de colonne sont définis en tant que « Prop\_<0-N> ». AvroFormat : permet d’utiliser les types et les noms de colonne prédéfinis dans le fichier Avro.
| BlobSink | Les types de données définis dans la section Structure de la définition de la table sont ignorés. Les types de données définis dans le magasin de données d'entrée sous-jacent sont utilisés. Les colonnes sont spécifiées en tant que colonnes de type Nullable pour la sérialisation Avro. |
| AzureTableSource | Les types de données définis dans la section Structure de la définition de la table sont ignorés. Les types de données définis dans la table Azure sous-jacente sont utilisés. |
| AzureTableSink | Les types de données définis dans la section Structure de la définition de la table sont ignorés. Les types de données définis dans le magasin de données d'entrée sous-jacent sont utilisés. |

**Remarque :** le service de Table Azure prend uniquement en charge un ensemble limité de types de données. Pour en savoir plus, veuillez consulter la rubrique [Présentation du modèle de données du service de Table][azure-table-data-type].

## Appel d’une procédure stockée pour un récepteur SQL
Quand vous copiez des données dans SQL Server ou Azure SQL Database, une procédure stockée spécifiée par l'utilisateur peut être configurée et appelée avec des paramètres supplémentaires.
### Exemple
1. Définissez le JSON de la table de sortie comme suit (prendre la table de base de données SQL Azure comme exemple) :

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. Définissez la section **SqlSink** dans l'activité de copie JSON comme suit. Pour appeler une procédure stockée lors de l’insertion des données, les deux propriétés **SqlWriterStoredProcedureName** et **SqlWriterTableType** sont requises.

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. Dans votre base de données, définissez la procédure stockée portant le même nom que **SqlWriterStoredProcedureName**. Elle gère les données d'entrée à partir de la source que vous avez spécifiée et les insère dans la table de sortie. Notez que le nom du paramètre de la procédure stockée doit être le même que le **tableName** défini dans le fichier de Table JSON.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. Dans votre base de données, définissez le type de table portant le même nom que **SqlWriterTableType**. Notez que le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

La fonction de procédure stockée tire parti des [paramètres Table-Valued][table-valued-parameters].

## Spécifiez l’encodage pour les fichiers texte.
Même si l'encodage UTF-8 est très populaire, les fichiers texte d’objet blob Azure utilisent souvent d’autres encodages pour des raisons historiques. La propriété **encodingName** vous permet de spécifier le l’encodage par nom de page de code pour les tables de type TextFormat. Pour obtenir une liste des noms de d’encodage valides, consultez : Propriété Encoding.EncodingName. Par exemple : windows-1250 ou shift\_jis. La valeur par défaut est : UTF-8. Consultez [Classe d’encodage](https://msdn.microsoft.com/library/system.text.encoding.aspx) pour des noms d’encodage valides.

## Voir aussi

- [Activité de copie : informations de référence sur la création de scripts JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vidéo : Présentation de l'activité de copie dans Azure Data Factory][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/fr-fr/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png
 

<!---HONumber=August15_HO7-->