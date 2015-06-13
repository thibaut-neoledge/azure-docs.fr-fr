<properties 
	pageTitle="Copie de données avec Azure Data Factory" 
	description="Découvrez comment utiliser l'activité de copie dans Azure Data Factory pour copier des données d'une source de données à une autre." 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Copie de données avec Azure Data Factory (activité de copie)
## Vue d'ensemble
Vous pouvez utiliser l'**activité de copie** dans un pipeline pour copier les données d'une source vers un récepteur (destination) au sein d'un lot. L'activité de copie peut être utilisée dans les scénarios suivants :

- **Entrée vers Azure**. Dans ce scénario, les données sont copiées à partir d'une source de données locale (par exemple, un serveur SQL) vers un magasin de données Azure (par exemple : un objet blob Azure, une table Azure ou une base de données SQL Azure) pour les scénarios secondaires suivants :
	- Collecte de données dans un emplacement centralisé sur Azure pour un traitement ultérieur.
	- Migration de données à partir de plateformes locales ou de clouds non-Azure vers Azure.
	- Archivage ou sauvegarde de données sur Azure pour un stockage hiérarchisé économique.
- **Sortie depuis Azure**. Dans ce scénario, les données sont copiées depuis Azure (par exemple : un objet blob Azure, une table Azure ou une base de données SQL Azure) vers des mini-Data Warehouse et un entrepôt de données locaux (par exemple, un serveur SQL) pour les scénarios secondaires suivants :
	- Transfert de données vers des systèmes locaux à cause d'un manque de prise en charge d'entrepôt de données sur le cloud.
	- Transfert de données vers des systèmes locaux pour tirer parti d'une solution locale ou d'une infrastructure de création de rapports existante.
	- Archive ou sauvegarde de données vers un système local pour un stockage hiérarchisé
- **Copie interne à Azure**. Dans ce scénario, les données distribuées sur des sources de données Azure sont regroupées dans un magasin de données Azure centralisé. Exemples : d'une table Azure vers un objet blob Azure, d'une table Azure vers SQL Azure, d'un objet blob Azure vers SQL Azure.

Pour en savoir plus, vous pouvez :

- Regarder la vidéo [Présentation de l'activité de copie dans Azure Data Factory][copy-activity-video]
- Consultez [Prise en main d'Azure Data Factory][adfgetstarted] pour parcourir un didacticiel vous permettant de copier des données à partir d'un stockage d'objets blob Azure vers une base de données SQL Azure, en utilisant l'activité de copie. 
- Consultez [Utilisation des pipelines avec des données locales][use-onpremises-datasources] pour suivre une procédure pas à pas permettant de copier des données à partir d'une base de données SQL Server locale vers un stockage d'objets blob Azure à l'aide de l'activité de copie.


## Sources et récepteurs pris en charge
L'activité de copie prend en charge les scénarios de déplacement de données suivants :

- Copie de données depuis un objet blob Azure vers un objet blob Azure, une table Azure, une base de données SQL Azure, un serveur SQL local ou un serveur SQL sur IaaS.
- Copie de données depuis une base de données SQL Azure vers un objet blob Azure, une table Azure, une base de données SQL Azure, un serveur SQL local ou un serveur SQL sur IaaS.
- Copie de données depuis une table Azure vers un objet blob Azure, une table Azure ou une base de données SQL Azure.
- Copie de données depuis un serveur SQL local/serveur SQL sur IaaS vers un objet blob Azure ou une base de données SQL Azure.
- Copie de données depuis une base de données Oracle locale vers un objet blob Azure
- Copie de données depuis un système de fichiers local vers un objet blob Azure
 

<table border="1">	
	<tr>
		<th><i>Source/Récepteur<i></th>
		<th>Objets blob Azure</th>
		<th>Table Azure</th>
		<th>Base de données SQL Azure</th>
		<th>SQL Server local</th>
		<th>SQL Server sur IaaS</th>
	</tr>	

	<tr>
		<td><b>Objet blob Azure</b></td>
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
		<td>X</td>
		<td>X</td>
	</tr>


	<tr>
		<td><b>Serveur SQL local</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Serveur SQL sur IaaS</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Système de fichiers local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Base de données Oracle locale</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>


</table>

### SQL sur une infrastructure en tant que service (IaaS)
SQL Server sur IaaS est également pris en charge comme source et le récepteur. Passerelle de gestion des données est requise lors de la création d'un service lié à SQL Server sur IaaS. Vous devez envisager d'installer la passerelle de gestion des données sur un ordinateur virtuel autre que l'un hébergement SQL Server afin d'éviter la dégradation des performances en raison de SQL Server et la passerelle en concurrence pour les ressources. Pour plus d'informations sur la passerelle de gestion des données, consultez [Permettre à vos pipelines d'accéder à des données locales][use-onpremises-datasources].

1.	Machine virtuelle avec un nom DNS public et un port public statique : mappage de port privé
2.	Machine virtuelle avec un nom DNS public sans point de terminaison SQL exposé
3.	Réseau virtuel
	<ol type='a'>
<li>Réseau privé virtuel de cloud Azure doté de la topologie suivante en fin de liste. </li>	
<li>Machine virtuelle avec un réseau privé virtuel de site à site (ou d'emplacement local au cloud) utilisant Azure Virtual Network.</li>	
</ol>![Data Factory avec l'activité de copie][image-data-factory-copy-actvity]

## Composants de l'activité de copie
L'activité de copie contient les composants suivants :

- **Table d'entrée**. Une table est un jeu de données ayant un schéma rectangulaire. La table d'entrée décrit les données d'entrée de l'activité, notamment : le nom et le type de la table, ainsi que le service lié qui fait référence à une source de données contenant les données d'entrée.
- **Table de sortie**. La table de sortie décrit les données de sortie de l'activité, notamment : le nom et le type de la table, ainsi que le service lié qui fait référence à une source de données contenant les données de sortie.
- **Règles de transformation**. Les règles de transformation spécifient la méthode d'extraction des données d'entrée à partir de la source, ainsi que la méthode de chargement des données de sortie vers le récepteur, etc.
 
Une activité de copie peut avoir une **table d'entrée** et une **table de sortie**.

## <a name="CopyActivityJSONSchema"></a>JSON de l'activité de copie
Un pipeline se compose d'une ou plusieurs activités. Les activités des pipelines sont définies dans la section **Activités[]**. Le JSON d'un pipeline est constitué comme suit :
         
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

Chaque activité dans la section **Activités** possède la structure générale suivante. La propriété **Type** doit être définie sur **CopyActivity**. L'activité de copie ne peut avoir qu'une seule table d'entrée et une seule table de sortie.
         

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
		<td>Spécifie le type de l'activité. <br/><br/>Le <b>type</b> doit être défini sur <b>CopyActivity</b>.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tables d'entrée utilisées par l'activité. Spécifiez une seule table d'entrée pour l'activité de copie.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tables de sortie utilisées par l'activité. Spécifiez une seule table de sortie pour l'activité de copie.</td>
		<td>O</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Les propriétés de la transformation dépendent du type. L'<b>activité de copie</b> nécessite la spécification d'une section <b>source</b> et d'une section <b>récepteur</b> au sein de la section <b>transformation</b>. Vous trouverez plus d'informations à ce sujet dans la suite de cet article. </td>
		<td>O</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Stratégies affectant le comportement d'exécution de l'activité. Si aucune valeur n'est spécifiée, les valeurs par défaut seront utilisées.</td>
		<td>N</td>
	</tr>


</table>

Pour plus d'informations sur les propriétés/balises JSON, consultez [Référence de script JSON][json-script-reference].

### types de sources et de récepteurs
Pour obtenir la liste des types de sources et de récepteurs, ainsi que des propriétés prises en charge par ces types, consultez la rubrique [Sources et récepteurs pris en charge][msdn-supported-sources-sinks] dans MSDN Library.

## Exemple d'activité de copie
Dans cet exemple, une table d'entrée et une table de sortie sont définies puis utilisées dans une activité de copie au sein d'un pipeline copiant des données à partir d'une base de données SQL Server locale vers un objet blob Azure.

**Hypothèses** Les artefacts Azure Data Factory suivants sont référencés dans les exemples de scripts JSON suivants :

* Groupe de ressources nommé **ADF**.
* Fabrique de données Azure nommée **CopyFactory**.
* Service lié nommé **MyOnPremisesSQLDB** pointant vers une base de données SQL Server locale.
* Service lié nommé **MyAzureStorage** pointant vers un stockage d'objets blob Azure.

### Table d'entrée JSON
Le script JSON suivant définit une table d'entrée qui fait référence à la table SQL **MyTable**, qui se trouve dans une base de données SQL Server locale définie par le service lié **MyOnPremisesSQLDB**. Notez que **name** correspond au nom de la table Azure Data Factory et **tableName** au nom de la table SQL dans une base de données SQL Server.

         
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

L'exemple de commande Azure PowerShell suivant utilise **New-AzureDataFactoryTable** qui utilise un fichier JSON contenant le script précédent pour créer une table (**MyOnPremTable**) dans une fabrique de données Azure : **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Pour plus d'informations sur les applets de commande Data Factory, consultez la [page de référence des applets de commande][cmdlet-reference].

### Table de sortie JSON
Le script JSON suivant définit la table de sortie **MyDemoBlob**, qui fait référence à l'objet blob Azure **MyBlob** situé dans le dossier d'objets blob **MySubFolder**, lui-même situé dans le conteneur d'objets blob **MyContainer**.
         
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

L'exemple de commande Azure PowerShell suivant utilise **New-AzureDataFactoryTable** qui utilise un fichier JSON contenant le script précédent pour créer une table (**MyDemoBlob**) dans une fabrique de données Azure : **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


### Pipeline (avec activité de copie) JSON
Dans cet exemple, le pipeline **CopyActivityPipeline** est défini à l'aide des propriétés suivantes :

- La propriété **type** est définie sur **CopyActivity**.
- **MyOnPremTable** est spécifié en tant que balise d'entrée (**inputs**).
- **MyAzureBlob** est spécifié en tant que balise de sortie (**outputs**) 
- La section **Transformation** contient deux sections secondaires : **source** et **récepteur**. Le type de la source est défini sur **SqlSource**, tandis que celui du récepteur est défini sur **BlobSink**. **sqlReaderQuery** définit la transformation (projection) qui doit être effectuée sur la source. Pour plus d'informations sur toutes les propriétés, consultez [Référence de script JSON][json-script-reference].

         
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


 L'exemple de commande Azure PowerShell suivant utilise **New-AzureDataFactoryPipeline** qui utilise un fichier JSON contenant le script précédent pour créer un pipeline (**CopyActivityPipeline**) dans une fabrique de données Azure : **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]Pour obtenir des exemples d'utilisation de l'activité de copie, consultez [Exemples d'utilisation de l'activité de copie dans Azure Data Factory][copy-activity-examples] 

## Sécurité
Cette section comprend des instructions et des méthodes conseillées concernant la sécurité globale, qui permettent d'établir un accès sécurisé aux magasins de données pour l'activité de copie.

Pour les magasins de données qui autorisent les connexions HTTPS, choisissez la connexion HTTPS pour que l'activité de copie établisse une communication sécurisée sur le réseau. Par exemple, pour **Azure Storage**, utilisez **DefaultEndpointsProtocol=https** dans la chaîne de connexion.

Pour **Azure SQL Database**, demandez explicitement une connexion chiffrée et ne faites pas confiance aux certificats de serveur pour éviter une attaque de l'intercepteur (« man in the middle »). Pour ce faire, utilisez **Encrypt=True** et **TrustServerCertificate=False** dans la chaîne de connexion. Pour plus d'informations, consultez [Instructions de sécurité et limitations d'Azure SQL Database](https://msdn.microsoft.com/library/azure/ff394108.aspx).

Pour les bases de données classiques telles que **SQL Server**, en particulier quand les instances se trouvent sur une machine virtuelle Azure, activez l'option de connexion chiffrée en configurant un certificat signé, avec **Encrypt=True** et **TrustServerCertificate=False** dans la chaîne de connexion. Pour plus d'informations, consultez [Activer les connexions chiffrées dans le moteur de base de données] (https://msdn.microsoft.com/library/ms191192(v=sql.110).aspx) et [Syntaxe de chaîne de connexion](https://msdn.microsoft.com/library/ms254500.aspx).

## Scénarios avancés
- **Filtrage de colonne à l'aide de la définition de structure**. En fonction du type de table, il est possible de spécifier un sous-ensemble de colonnes à partir de la source en indiquant un nombre de colonnes dans la définition de **Structure** de la définition de table inférieur à celles qui se trouvent dans la source de données sous-jacente.
- **Règles de transformation : mappage de colonnes**. Le mappage de colonne peut spécifier la façon dont les colonnes de la table source sont mappées vers les colonnes de table du récepteur.
- **Gestion des types de données par l'activité de copie**. Explique dans quels cas les types de données spécifiés dans la section Structure de la définition de la table sont honorés/ignorés.
- **Appeler une procédure stockée pour un récepteur SQL**. Quand vous copiez des données dans SQL Server ou Azure SQL Database, une procédure stockée spécifiée par l'utilisateur peut être configurée et appelée.

Pour plus d'informations sur ces scénarios, consultez l'article [Scénarios avancés pour l'utilisation de l'activité de copie avec Azure Data Factory][copy-activity-advanced].

## Procédures pas à pas
Consultez [Prise en main d'Azure Data Factory][adfgetstarted] pour parcourir un didacticiel vous permettant de copier des données à partir d'un stockage d'objets blob Azure vers une base de données SQL Azure, en utilisant l'activité de copie.
 
Consultez [Utilisation des pipelines avec des données locales][use-onpremises-datasources] pour suivre une procédure pas à pas permettant de copier des données à partir d'une base de données SQL Serveur locale vers un stockage d'objets blob Azure à l'aide de l'activité de copie.

## Voir aussi
- [Exemples d'activité de copie][copy-activity-examples]
- [Vidéo : Présentation de l'activité de copie dans Azure Data Factory][copy-activity-video]
- [Rubrique Activité de copie sur MSDN Library][msdn-copy-activity]
- [Scénarios avancés pour l'utilisation de l'activité de copie avec Azure Data Factory][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!---HONumber=GIT-SubDir--> 