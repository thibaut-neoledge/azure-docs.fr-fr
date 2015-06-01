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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Scénarios avancés pour l'utilisation de l'activité de copie avec Azure Data Factory 
## Vue d'ensemble
Vous pouvez utiliser l'**activité de copie** dans un pipeline pour copier les données d'une source vers un récepteur (destination) au sein d'un lot. Cette rubrique décrit les scénarios avancés pris en charge par l'activité de copie. Pour une présentation détaillée de l'activité de copie et des principaux scénarios pris en charge, consultez la rubrique [Copier des données avec Azure Data Factory][adf-copyactivity].


## Filtrage de colonne à l'aide de la définition de structure
En fonction du type de table, il est possible de spécifier un sous-ensemble de colonnes à partir de la source en indiquant un nombre de colonnes dans la définition de **Structure** de la définition de table inférieur à ceux qui se trouvent dans la source de données sous-jacente. Le tableau suivant fournit des informations sur la logique de filtrage de colonne pour différents types de table.

<table>

	<tr>
		<th align="left">Type de table</th>
		<th align="left">Logique de filtrage de colonne</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>La définition de <b>Structure</b> dans la table JSON doit correspondre à la structure de l'objet blob. Pour sélectionner un sous-ensemble des colonnes, utilisez la fonctionnalité de mappage de colonnes décrite dans la section suivante, Règles de transformation&#160;: mappage de colonnes.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation et OnPremisesSqlServerTableLocation</td>
		<td align="left">
			Si la propriété <b>SqlReaderQuery</b> est spécifiée dans le cadre de la définition d'activité de copie, la définition de <b>Structure</b> de la table doit correspondre aux colonnes sélectionnées dans la requête.<br/><br/>
			Si la propriété <b>SqlReaderQuery</b> n'est pas spécifiée, l'activité de copie construit automatiquement une requête SELECT basée sur les colonnes spécifiées dans la définition de <b>Structure</b> de la définition de la table.
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			La section <b>Structure</b> de la définition de la table peut contenir un ensemble complet ou un sous-ensemble de colonnes dans la table Azure sous-jacente.
		</td>
	<tr>

</table>

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
Dans cet exemple, l'activité d'un pipeline est définie comme suit. Colonnes de la source mappées vers les colonnes du récepteur (columnMappings) en utilisant la propriété **Translator**.

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
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', SliceStart)"
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
		}
	}

![Mappage de colonne 2][image-data-factory-column-mapping-2]

## Gestion des types de données par l'activité de copie

Les types de données spécifiés dans la section Structure de la définition de table sont uniquement respectés pour **BlobSource**. Le tableau ci-dessous décrit la façon dont les types de données sont gérés pour les autres types de source et de récepteur.

<table>	
	<tr>
		<th align="left">Source/Récepteur</th>
		<th align="left">Logique de gestion du type de données</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de la table sont ignorés. Les types de données définis dans la base de données SQL sous-jacente sont utilisés pour l'extraction de données durant l'activité de copie.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de la table sont ignorés. Les types de données de la source et de la destination sous-jacentes sont comparés. Par ailleurs, une conversion de type implicite est effectuée s'il existe des incompatibilités de types.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td>Lors du transfert de <b>BlobSource</b> vers <b>BlobSink</b>, il n'existe aucune transformation de type&#160;; les types de données définis dans la section <b>Structure</b> de la définition de la table sont ignorés. Pour les autres destinations que <b>BlobSink</b>, les types de données définis dans la section <b>Structure</b> de la définition de table sont respectés.<br/><br/>
		Si la <b>Structure</b> n'est pas spécifiée dans la destination de table, la gestion du type dépend de la propriété <b>format</b> de la table <b>BlobSource</b>&#160;:
		<ul>
			<li> <b>TextFormat</b>&#160;: tous les types de colonne sont traités en tant que chaîne et tous les noms de colonne sont définis en tant que «&#160;Prop_&lt;0-N>&#160;»</li> 
			<li><b>AvroFormat</b>&#160;: permet d'utiliser les types de colonne intégrés et leurs noms dans le fichier Avro.</li> 
			<li><b>JsonFormat</b>&#160;: tous les types de colonne sont traités en tant que chaîne et utilisent les noms de colonnes intégrés dans le fichier Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de la table sont ignorés. Les types de données définis dans le magasin de données d'entrée sous-jacent sont utilisés. Les colonnes sont spécifiées en tant que colonnes de type Nullable pour la sérialisation Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de la table sont ignorés. Les types de données définis dans la table Azure sous-jacente sont utilisés.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de la table sont ignorés. Les types de données définis dans le magasin de données d'entrée sous-jacent sont utilisés.</td>
	</tr>

</table>

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
Même si l'encodage UTF-8 est très populaire, les fichiers texte d’objet blob Azure utilisent souvent d’autres encodages pour des raisons historiques. La propriété **encodingName** vous permet de spécifier le l’encodage par nom de page de code pour les tables de type TextFormat. Pour obtenir une liste des noms de d’encodage valides, consultez : Propriété Encoding.EncodingName. Par exemple : windows-1250 ou shift_jis. La valeur par défaut est : UTF-8. Consultez la rubrique [Classe d’encodage](https://msdn.microsoft.com/library/system.text.encoding(v=vs.110).aspx) pour obtenir les noms d’encodage valides.

## Voir aussi

- [Exemples d'utilisation de l’activité de copie][copy-activity-examples]
- [Copie de données avec Azure Data Factory][adf-copyactivity]
- [Activité de copie : informations de référence sur la création de scripts JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vidéo : Présentation de l'activité de copie dans Azure Data Factory][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/fr-fr/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=54-->