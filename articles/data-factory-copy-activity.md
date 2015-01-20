<properties title="Copy data with Azure Data Factory" pageTitle="Copie de données avec Azure Data Factory" description="Découvrez comment utiliser l'activité de copie dans Azure Data Factory pour copier des données d'une source de données à une autre." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Copie de données avec Azure Data Factory (activité de copie)
Vous pouvez utiliser l'**activité de copie** dans un pipeline pour copier des données d'une source vers un récepteur (destination) au sein d'un lot. L'activité de copie peut être utilisée dans les scénarios suivants :

- **Entrée vers Azure**. Dans ce scénario, les données sont copiées depuis une source de données locale (par exemple : SQL Server) vers un magasin de données Azure (par exemple : objet blob Azure, table Azure ou Azure SQL Database) pour les scénarios secondaires suivants :
	- Collecte de données dans un emplacement centralisé sur Azure pour un traitement ultérieur.
	- Migration de données à partir de plateformes locales ou de clouds non-Azure vers Azure.
	- Archivage ou sauvegarde de données sur Azure pour un stockage hiérarchisé économique.
- **Sortie depuis Azure**. Dans ce scénario, les données sont copiées depuis Azure (par exemple : objet blob Azure, table Azure ou base de données SQL Azure) vers des datamarts et des entrepôts de données (par exemple : un serveur SQL) pour les scénarios secondaires suivants :
	- Transfert de données vers des systèmes locaux à cause d'un manque de prise en charge d'entrepôt de données sur le cloud.
	- Transfert de données vers des systèmes locaux pour tirer parti d'une solution locale ou d'une infrastructure de création de rapports existante.
	- Archive ou sauvegarde de données vers un système local pour un stockage hiérarchisé
- **Copie interne à Azure**. Dans ce scénario, les données distribuées sur des sources de données Azure sont regroupées dans un magasin de données Azure centralisé. Exemples : table Azure vers un objet blob Azure, objet blob Azure vers une table Azure, table Azure vers SQL Azure, objet blob Azure vers SQL Azure.

Consultez [Prise en main d'Azure Data Factory][adfgetstarted] pour parcourir un didacticiel vous permettant de copier des données à partir d'un stockage d'objets blob Azure vers une base de données SQL Azure, en utilisant l'activité de copie. Consultez [Utilisation des pipelines avec des données locales][use-onpremises-datasources] pour suivre une procédure pas à pas permettant de copier des données à partir d'une base de données SQL Serveur locale vers un stockage d'objets blob Azure à l'aide de l'activité de copie.


## Composants de l'activité de copie
L'activité de copie contient les composants suivants : 

- **Table d'entrée**. Une table est un jeu de données ayant un schéma rectangulaire. Le composant de table d'entrée 
- décrit les données d'entrée de l'activité, notamment : le nom et le type de la table, ainsi que le service lié qui fait référence à une source de données contenant les données d'entrée.
- **Table de sortie**. La table de sortie décrit les données de sortie de l'activité, notamment : le nom et le type de la table, ainsi que le service lié qui fait référence à une source de données contenant les données de sortie.
- **Règles de transformation**. Les règles de transformation spécifient la méthode d'extraction des données d'entrée à partir de la source, ainsi que la méthode de chargement des données de sortie vers le récepteur, etc.
 
Une activité de copie peut avoir une **table d'entrée** et une **table de sortie**.

## JSON de l'activité de copie
Un pipeline se compose d'une ou plusieurs activités. Les activités des pipelines sont définies dans la section **activities []**. Le JSON d'un pipeline est constitué comme suit :
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Chaque activité de la section **activities** possède la structure de premier niveau suivante. La propriété **type** doit avoir la valeur **CopyActivity**. L'activité de copie ne peut avoir qu'une seule table d'entrée et une seule table de sortie.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

Le tableau suivant décrit les balises utilisées par une section de l'activité. 

<table border="1">	
	<tr>
		<th align="left">Balise</th>
		<th align="left">Description</th>
		<th align="left">Requis</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Nom de l'activité.</td>
		<td>O</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Texte décrivant la façon dont l'activité est utilisée.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Spécifie le type de l'activité. <br/><br/>Le <b>type</b> doit avoir la valeur <b>CopyActivity</b>.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tables d'entrée utilisées par l'activité.  Spécifiez une seule table d'entrée pour l'activité de copie.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tables de sortie utilisées par l'activité.  Spécifiez une seule table de sortie pour l'activité de copie.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Les propriétés de la transformation dépendent du type.  L' <b>activité de copie</b> vous oblige à spécifier une <b>section source</b> et une <b>section sink</b> dans la section <b>transformation</b> . Vous trouverez plus d'informations à ce sujet dans la suite de cet article. </td>
		<td>O</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Stratégies affectant le comportement d'exécution de l'activité. Si aucune valeur n'est spécifiée, les valeurs par défaut seront utilisées.</td>
		<td>N</td>
	</tr>


</table>

Pour plus d'informations sur les propriétés/balises JSON, consultez [Référence de script JSON][json-script-reference].

## Exemple d'activité de copie
Dans cet exemple, une table d'entrée et une table de sortie sont définies puis utilisées dans une activité de copie au sein d'un pipeline copiant des données à partir d'une base de données SQL Server locale vers un objet blob Azure.

**Hypothèses**
Les artefacts Azure Data Factory suivants sont référencés dans les exemples de scripts JSON suivants :

* Groupe de ressources nommé **ADF**.
* Fabrique de données Azure nommée **CopyFactory**.
* Service lié nommé **MyOnPremisesSQLDB** pointant vers une base de données SQL Server locale.
* Service lié nommé **MyAzureStorage** pointant vers un stockage d'objets blob Azure.

### Table d'entrée JSON
Le script JSON suivant définit une table d'entrée faisant référence à une table SQL : **MyTable** dans une base de données SQL Server locale définie par le service **MyOnPremisesSQLDB** lié. Notez que **name** correspond au nom de la table Azure Data Factory et **tableName** au nom de la table SQL dans une base de données SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
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

L'exemple de commande Azure PowerShell suivant utilise **New-AzureDataFactoryTable** qui utilise un fichier JSON contenant le script précédent pour créer une table (**MyOnPremTable**) dans une fabrique de données Azure : **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Pour plus d'informations sur les applets de commande Data Factory, consultez la page de [référence des applets de commande][cmdlet-reference]. 

### Table de sortie JSON
Le script JSON suivant définit une table de sortie : **MyDemoBlob**, qui fait référence à un objet blob Azure : **MyBlob** dans le dossier d'objets blob : **MySubFolder** dans le conteneur d'objets blob : **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
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

L'exemple de commande Azure PowerShell suivant utilise **New-AzureDataFactoryTable** qui utilise un fichier JSON contenant le script précédent pour créer une table (**MyDemoBlob**) dans une fabrique de données Azure : **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### Pipeline (avec activité de copie) JSON
Dans cet exemple, un pipeline : **CopyActivityPipeline** est défini avec les propriétés suivantes : 

- La propriété **type** a la valeur **CopyActivity**.
- **MyOnPremTable** est spécifié en tant qu'entrée (balise **inputs**).
- **MyAzureBlob** est spécifié en tant que sortie (balise **outputs**)
- La section **Transformation** contient deux sous-sections : **source** et **sink**. Le type de la source a la valeur **SqlSource**, alors que le type du récepteur a la valeur **BlobSink**. **sqlReaderQuery** définit la transformation (projection) à effectuer sur la source. Pour plus d'informations sur toutes les propriétés, consultez [Référence de script JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
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
        		]
    		}
		}


 L'exemple de commande Azure PowerShell suivant utilise **New-AzureDataFactoryPipeline** qui utilise un fichier JSON contenant le script précédent pour créer un pipeline (**CopyActivityPipeline**) dans une fabrique de données Azure : **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## Entrées et sorties prises en charge
L'exemple précédent utilisait SqlSource comme source et BlobSink comme récepteur dans sa section de transformation. Le tableau suivant répertorie les sources et récepteurs pris en charge par l'activité de copie. 

<table border="1">	
	<tr>
		<th><i>Récepteur/Source<i></th>
		<th>Objets blob Azure</th>
		<th>Table Azure</th>
		<th>Base de données SQL Azure</th>
		<th>SQL Server local</th>
		<th>SQL Server sur IaaS</th>
	</tr>	

	<tr>
		<td><b>Objets blob Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Table Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Base de données SQL Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>SQL Server local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server sur IaaS</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


Le tableau suivant répertorie les types de source et de récepteur qui peuvent être utilisés dans un fichier JSON pour un pipeline contenant une activité de copie.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">Type de source</th>
		<th align="left">Type de récepteur</th>
	</tr>	

	<tr>
		<td><b>Objets blob Azure</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Table Azure</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Base de données SQL Azure</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL Server local</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL sur IaaS</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

Le tableau suivant répertorie les propriétés prises en charge par ces sources et récepteurs.

<table border="1">

	<tr>
	<th align="left">Source/Récepteur</th>
	<th align="left">Propriété prise en charge</th>
	<th align="left">Description</th>
	<th align="left">Valeurs autorisées</th>
	<th align="left">Requis</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Spécifie s'il faut traiter une chaîne null ou vide en tant que valeur null.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>Indique le nombre de lignes à ignorer.</td>
		<td>Entier compris entre 0 et Max.</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>Utilise la requête personnalisée pour lire des données.</td>
		<td>Chaîne de requête de table Azure.<br/>Exemple : " ColumnA eq ValueA "</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Indique si l'exception doit être ignorée : " la table n'existe pas ".</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>Utilise la requête personnalisée pour lire des données.</td>
		<td>Chaîne de requête SQL.<br/><br/> Par exemple : "Select * from MyTable".<br/><br/> Si aucune valeur n'est spécifiée, sélectionnez <colonnes définies dans la structure> à partir de la requête MyTable.</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>Valeur de clé de partition par défaut qui peut être utilisée par le récepteur.</td>
		<td>Valeur de chaîne.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>Nom de colonne spécifié par l'utilisateur, dont les valeurs de colonne sont utilisées comme clé de partition.<br/><br/> Si aucune valeur n'est spécifiée, AzureTableDefaultPartitionKeyValue est utilisée comme clé de partition.</td>
		<td>Nom de colonne.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>Valeurs de colonne du nom de colonne spécifié à utiliser comme clé de ligne.<br/><br/>Si aucune valeur n'est spécifiée, un GUID est utilisé pour chaque ligne.</td>
		<td>Nom de colonne.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>Mode d'insertion des données dans une table Azure.</td>
		<td>" merge "<br/><br/>" replace "</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>Insère des données dans la table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte.</td>
		<td>Entier compris entre 1 et 100 (unité = nombre de lignes)</td>
		<td>N<br/><br/>(Valeur par défaut = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Insère des données dans la table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte</td>
		<td>(Unité = intervalle de temps)<br/><br/>Exemple : " 00:20:00 " (20 minutes)</td>
		<td>N<br/><br/>(Valeur par défaut du délai d'attente du stockage client par défaut : 90 secondes)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>Spécifie le nom de la procédure stockée vers les données upsert (mise à jour/insertion) dans la table cible.</td>
		<td>Nom de procédure stockée.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>Spécifie le type de table à utiliser dans la procédure stockée précédente.</td>
		<td>Nom de type de table.</td>
		<td>N</td>
	</tr>
</table>

### SQL sur une infrastructure en tant que service (IaaS)
Pour SQL sur IaaS, Azure est pris en charge en tant que fournisseur IaaS. Les topologies VPN et le réseau qui suivent sont pris en charge. Notez que la passerelle de gestion des données est nécessaire pour les cas n° 2 et n° 3, mais pas pour le cas n° 1. Pour plus d'informations sur la passerelle de gestion des données, consultez [Permettre à vos pipelines d'accéder à des données locales][use-onpremises-datasources].

1.	Machine virtuelle avec un nom DNS public et un port public statique : mappage de port privé
2.	Machine virtuelle avec un nom DNS public sans point de terminaison SQL exposé
3.	Réseau virtuel
	<ol type='a'>
	<li>Azure Cloud VPN with following topology at the end of the list. </li>	
	<li>VM with onpremises-to-cloud site-to-site VPN using Azure Virtual Network.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Column filtering using structure definition
Depending on the type of Table, it is possible to specify a subset of the columns from the source by specifying fewer columns in the **Structure** definition of the table definition than the ones that exist in the underlying data source. The following table provides information about column filtering logic for different types of table. 

<table>

	<tr>
		<th align="left">Type de table</th>
		<th align="left">Logique de filtrage de colonne</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>La définition de <b>Structure</b> dans la table JSON doit correspondre à la structure de l'objet blob.  Pour sélectionner un sous-ensemble des colonnes, utilisez la fonctionnalité de mappage de colonnes décrite dans la section suivante : Règles de transformation : mappage de colonnes.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation et OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Si la propriété <b>SqlReaderQuery</b> est spécifiée dans le cadre de la définition de l'activité de copie, la définition de <b>Structure</b> de la table doit correspondre aux colonnes sélectionnées dans la requête.</p>
			<p>Si la propriété <b>SqlReaderQuery</b> n'est pas spécifiée, l'activité de copie construit automatiquement une requête SELECT basée sur les colonnes spécifiées dans la définition de <b>Structure</b> de la définition de table.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			La section <b>Structure</b> de la définition de table peut contenir un ensemble complet ou un sous-ensemble des colonnes dans la table Azure sous-jacente.
		</td>
	<tr>

</table> 

## Règles de transformation : mappage de colonnes
Le mappage de colonne peut spécifier la façon dont les colonnes de la table source sont mappées vers les colonnes de table du récepteur. Cette méthode prend en charge les scénarios suivants :

- Mappage de toutes les colonnes de la table source " structure " vers la table de destination " structure ".
- Un sous-ensemble de colonnes de la table source " structure " est mappé vers toutes les tables de destination " structure ".

Les éléments suivants ne sont pas pris en charge, ce qui lève une exception : 

- Un nombre de colonnes inférieur ou supérieur dans la destination.
- Mappage en double.
- Le résultat de la requête SQL n'a pas de nom de colonne

#### Exemple 1 : mappage de colonne de SQL Server vers un objet blob Azure
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

Si vous ne spécifiez pas de **fileName** pour une **table d'entrée**, tous les fichiers/objets blob du répertoire d'entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Consultez les exemples de fichiers dans le [didacticiel][adf-tutorial] pour plus d'informations.

Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple  : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Pour définir **folderPath** et **fileName** de manière dynamique en fonction de l'heure de **SliceStart**, utilisez la propriété **partitionedBy**. Dans l'exemple suivant, **folderPath** utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Dans cet exemple, l'activité d'un pipeline est définie comme suit. Les colonnes de la source sont mappées aux colonnes du récepteur (**columnMappings**) via la propriété **Translator**.

##### Exemple : définition du mappage de colonnes

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

![Column Mapping][image-data-factory-column-mapping-1]

##### Exemple 2 : mappage de colonne avec une requête SQL à partir de SQL Server vers un objet blob Azure
Dans cet exemple, une requête SQL (par opposition à la table dans l'exemple précédent) est utilisée pour extraire des données à partir d'un serveur SQL Server local et les colonnes provenant des résultats de la requête sont mappés vers un artefact source, puis vers l'artefact de destination. Pour les besoins de cet exemple, la requête renvoie 5 colonnes.

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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
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

![Column Mapping 2][image-data-factory-column-mapping-2]

#### Gestion des types de données par l'activité de copie

Les types de données spécifiés dans la section Structure de la définition de table sont uniquement respectés pour **BlobSource**.  Le tableau ci-dessous décrit la façon dont les types de données sont gérés pour les autres types de source et de récepteur.

<table>	
	<tr>
		<th align="left">Source/Récepteur</th>
		<th align="left">Logique de gestion du type de données</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de table sont ignorés.  Les types de données définis dans la base de données SQL sous-jacente sont utilisés pour l'extraction de données durant l'activité de copie.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de table sont ignorés.  Les types de données de la source et de la destination sous-jacentes sont comparés. Par ailleurs, une conversion de type implicite est effectuée s'il existe des incompatibilités de types.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>Durant le transfert de <b>BlobSource</b> à <b>BlobSink</b>, il n'existe aucune transformation de type. Les types définis dans la section <b>Structure</b> de la définition de table sont ignorés.  Pour les destinations autres que <b>BlobSink</b>, les types de données définis dans la section <b>Structure</b> de la définition de table sont respectés.</p>
		<p>
		Si la définition de <b>Structure</b> n'est pas spécifiée dans la définition de table, la gestion des types dépend de la propriété <b>format</b> de <b>BlobSink</b> :
		</p>
		<ul>
			<li> <b>TextFormat :</b> tous les types de colonne sont traités en tant que chaîne et tous les noms de colonne sont définis en tant que " Prop_<0-N> "</li> 
			<li><b>AvroFormat :</b> permet d'utiliser les types de colonne intégrés et leurs noms dans le fichier Avro.</li> 
			<li><b>JsonFormat :</b> tous les types de colonne sont traités en tant que chaîne et utilisent les noms de colonnes intégrés dans le fichier Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de table d'entrée sont ignorés.  Les types de données définis dans le magasin de données d'entrée sous-jacent sont utilisés.  Les colonnes sont spécifiées en tant que colonnes de type Nullable pour la sérialisation Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de table sont ignorés.  Les types de données définis dans la table Azure sous-jacente sont utilisés.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Les types de données définis dans la section <b>Structure</b> de la définition de table sont ignorés.  Les types de données définis dans le magasin de données d'entrée sous-jacent sont utilisés.</td>
	</tr>

</table>


## Procédures pas à pas
Consultez [Prise en main d'Azure Data Factory][adfgetstarted] pour parcourir un didacticiel vous permettant de copier des données à partir d'un stockage d'objets blob Azure vers une base de données SQL Azure, en utilisant l'activité de copie.
 
Consultez [Utilisation des pipelines avec des données locales][use-onpremises-datasources] pour suivre une procédure pas à pas permettant de copier des données à partir d'une base de données SQL Serveur locale vers un stockage d'objets blob Azure à l'aide de l'activité de copie.



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=35.2-->
