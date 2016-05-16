<properties 
	pageTitle="Jeux de données dans Azure Data Factory | Microsoft Azure" 
	description="Comprendre les jeux de données Azure Data Factory et apprendre à les créer." 
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
	ms.date="04/08/2016" 
	ms.author="spelluru"/>

# Jeux de données dans Azure Data Factory
Les jeux de données dans Azure Data Factory englobent des références/liens nommés vers les données que vous souhaitez utiliser en tant qu’entrées ou sorties d’activités dans un pipeline. Les jeux de données identifient les données dans différents magasins de données, notamment des tables, des fichiers, des dossiers et des documents.

Lorsque vous créez une fabrique de données, vous créez des services liés qui lient des magasins de données à la fabrique de données. Un **service lié** définit les informations nécessaires à Azure Data Factory pour se **connecter** à un magasin de données. Par exemple : un compte de stockage Azure et Base de données SQL Microsoft Azure. Le service lié définit le mécanisme (adresse, protocole, schéma d’authentification, etc.) pour accéder au magasin de données.

Un **jeu de données** dans Data Factory représente des structures de données (par exemple : un conteneur d’objets blob ou une table SQL) dans le magasin de données, qui peuvent être utilisées en tant qu’entrées ou sorties d’une activité dans un pipeline. Après avoir créé des jeux de données, vous pouvez utiliser ces derniers avec des activités dans le pipeline. Un jeu de données peut, par exemple, constituer un jeu de données d’entrée/de sortie d’une activité de copie/activité HDInsightHive.

> [AZURE.NOTE] Si vous découvrez tout juste Azure Data Factory, consultez [Présentation du service Azure Data Factory](data-factory-introduction.md) pour une vue d’ensemble du service Azure Data Factory et le didacticiel [Créer votre première fabrique de données (vue d’ensemble)](data-factory-build-your-first-pipeline.md) pour créer votre première fabrique de données. Ces deux articles fournissent des informations de base pour mieux comprendre le présent article.

## Définir les jeux de données
Un jeu de données dans Azure Data Factory est défini comme suit :


	{
	    "name": "<name of dataset>",
	    "properties": {
	        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
			"external": <boolean flag to indicate external data. only for input datasets>,
	        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
	        "structure": [
	            {
	                "name": "<Name of the column>",
	                "type": "<Name of the type>"
	            }
	        ],
	        "typeProperties": {
	            "<type specific property>": "<value>",
				"<type specific property 2>": "<value 2>",
	        },
	        "availability": {
	            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
	            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
	        },
	       "policy": 
	        {      
	        }
	    }
	}

La table suivante décrit les propriétés dans le JSON ci-dessus :

| Propriété | Description | Requis | Default |
| -------- | ----------- | -------- | ------- |
| name | Nom du jeu de données Consultez la page [Azure Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour les règles d’affectation des noms. | Oui | N/D |
| type | Type du jeu de données. Spécifiez un des types pris en charge par Azure Data Factory (par exemple : AzureBlob, AzureSqlTable). <br/><br/>Consultez la rubrique [Type du jeu de données](#Type) pour plus d’informations. | Oui | N/D |
| structure | Schéma du jeu de données<br/><br/>Consultez la section [Structure d’un jeu de données](#Structure) pour plus de détails | Non. | N/D |
| typeProperties | Propriétés correspondant au type sélectionné. Consultez la section [Type du jeu de données](#Type) pour plus d’informations sur les types pris en charge et leurs propriétés. | Oui | N/D |
| external | Indicateur booléen pour indiquer si un jeu de données est explicitement généré par un pipeline de fabrique de données ou non. | Non | false | 
| availability | Définit la fenêtre de traitement ou le modèle de découpage pour la production du jeu de données. <br/><br/>Consultez la rubrique [Disponibilité du jeu de données](#Availability) pour plus de détails<br/><br/>Consultez l’article [Planification et exécution](data-factory-scheduling-and-execution.md) pour plus d’informations sur le modèle de découpage du jeu de données | Oui | N/D
| policy | Définit les critères ou la condition que les segments du jeu de données doivent remplir. <br/><br/>Consultez la rubrique [Stratégie du jeu de données](#Policy) pour plus de détails | Non | N/D |

### Exemple

Voici un exemple de jeu de données qui représente une table nommée **MyTable** dans une **base de données SQL Azure**.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

Notez les points suivants :

- Le type est défini sur AzureSQLTable.
- La propriété de type tableName (propre au type AzureSqlTable) est définie sur MyTable.
- linkedServiceName fait référence à un service lié de type AzureSqlDatabase. Voir la définition du service lié ci-dessous. 
- la fréquence de disponibilité (availability) est définie sur Day et l’intervalle sur 1, ce qui signifie que la tranche est exécutée quotidiennement.  

AzureSqlLinkedService est défini comme suit :

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}

Dans l’exemple JSON ci-dessus :

- type est défini sur AzureStorage
- la propriété de type connectionString spécifie les informations nécessaires pour vous connecter à une base de données SQL Azure.  


Comme vous pouvez le voir, le service lié définit comment se connecter à une base de données SQL Azure et le jeu de données définit quelle table est utilisée comme entrée/sortie de votre fabrique de données. La section d’activité de votre [pipeline](data-factory-create-pipelines.md) JSON spécifie si le jeu de données est utilisé comme jeu de données d’entrée ou de sortie.


> [AZURE.IMPORTANT] À moins qu’un jeu de données soit généré par Azure Data Factory, il doit être marqué comme étant **external**. Cela s’applique généralement aux entrées de la première activité d’un pipeline.

## <a name="Type"></a> Type du jeu de données
Les sources de données prises en charge et les types de jeux de données sont alignés. Consultez les rubriques référencées dans l’article [Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores) pour obtenir plus d’informations sur les types et la configuration des jeux de données. Par exemple : si vous utilisez des données à partir d’une base de données SQL Azure, cliquez sur Base de données SQL Microsoft Azure dans la liste des magasins de données pris en charge pour afficher des informations détaillées sur l’utilisation d’une base de données SQL Azure en tant que magasin de données source ou magasin de données récepteur.

## <a name="Structure"></a>Structure d'un jeu de données
La section **Structure** définit le schéma du jeu de données. Il contient une collection de noms et types de données de colonnes. Dans l’exemple suivant, le jeu de données contient trois colonnes : slicetimestamp, projectname et pageviews. Leurs types respectifs sont les suivants : String, String et Decimal.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Availability"></a> Disponibilité du jeu de données
La section **availability** (disponibilité) dans un jeu de données définit la fenêtre de traitement (horaire, journalier, hebdomadaire etc.) ou le modèle de découpage pour la production du jeu de données. Consultez l’article [Planification et exécution](data-factory-scheduling-and-execution.md) pour plus d'informations sur le découpage du jeu de données et le modèle de dépendance.

La section availability ci-dessous spécifie que le jeu de données est exécuté toutes les heures dans le cas d’un jeu de données de sortie ou qu’il est disponible toutes les heures dans le cas d’un jeu de données d’entrée.

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

Le tableau suivant décrit les propriétés que vous pouvez utiliser dans la section availability.

| Propriété | Description | Requis | Default |
| -------- | ----------- | -------- | ------- |
| frequency | Spécifie l’unité de temps pour la production du segment du jeu de données.<br/><br/>**Fréquence prise en charge** : Minute, Hour, Day, Week, Month | Oui | N/D |
| interval | Spécifie un multiplicateur de fréquence<br/><br/>« Fréquence x intervalle » détermine la fréquence à laquelle le segment est généré.<br/><br/>Si vous voulez que le jeu de données soit segmenté toutes les heures, définissez **Frequency** sur **Hour** et **Interval** sur **1**.<br/><br/>**Remarque :** si vous définissez la fréquence en minutes, nous vous recommandons de définir l’intervalle au minimum sur 15 | Oui | N/D |
| style | Spécifie si le segment doit être généré au début / à la fin de l’intervalle.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si la fréquence est définie sur Month et le style défini sur EndOfInterval, le segment est généré le dernier jour du mois. Si le style est défini sur StartOfInterval, le segment est généré le premier jour du mois.<br/><br/>Si la fréquence est définie sur Day et style défini sur EndOfInterval, le segment est généré durant la dernière heure du jour.<br/><br/>Si la fréquence est définie sur Hour et le style défini sur EndOfInterval, le segment est généré à la fin de l’heure. Par exemple, pour un segment de la période 13 h-14 h, le segment est généré à 14 h. | Non | EndOfInterval |
| anchorDateTime | Définit la position absolue dans le temps utilisée par le planificateur pour calculer les limites du segment du jeu de données.<br/><br/>**Remarque :** si AnchorDateTime a des parties de date qui sont plus granulaires que la fréquence, les parties les plus granulaires sont ignorées. <br/><br/>Par exemple, si **interval** est défini sur **hourly** (frequency : hour et interval : 1) et si **AnchorDateTime** contient **minutes et secondes**, les parties **minutes et secondes** de la valeur AnchorDateTime sont ignorées. | Non | 01/01/0001 |
| Offset | Intervalle selon lequel le début et la fin de tous les segments du jeu de données sont déplacés.<br/><br/>**Remarque :** si les valeurs anchorDateTime et offset sont spécifiées, le résultat correspond au décalage combiné. | Non | N/D |

### exemple offset

Segments quotidiens qui démarrent à 6h au lieu de minuit, la valeur par défaut.

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

**frequency** est défini sur **Month** et **interval** est défini sur **1** (une fois par mois) : si vous souhaitez que le segment soit produit le 9e jour de chaque mois à 6 h, définissez le décalage sur « 09.06:00:00 ». N’oubliez pas qu’il s’agit de l’heure UTC.

Pour un planning de 12 mois (fréquence = mois ; intervalle = 12), la valeur offset : 60.00:00:00 signifie chaque année le 1er ou le 2 mars (60 jours à partir du début de l’année si style = StartOfInterval), selon si l’année en cours est une année bissextile ou non.

### Exemple anchorDateTime

**Exemple :** segments du jeu de données de 23 heures qui démarrent le 2007-04-19T08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

### Exemple de décalage/style

Si vous avez besoin d’un jeu de données tous les mois à une date et une heure spécifiques (par exemple, le 3e jour de chaque mois à 8h), vous pouvez utiliser la balise **offset** pour définir la date et l’heure d’exécution.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}


## <a name="Policy"></a>Stratégie du jeu de données

La section **policy** de la définition du jeu de données définit les critères ou la condition que les segments du jeu de données doivent remplir.

### Stratégies de validation

| Nom de la stratégie | Description | Appliqué(e) à | Requis | Default |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valide le fait que les données dans un **objet blob Azure** répondent aux exigences de taille minimale (en mégaoctets). | Objets blob Azure | Non | N/D |
|minimumRows | Valide le fait que les données dans une **base de données SQL Azure** ou une **table Azure** contiennent le nombre minimal de lignes. | <ul><li>Base de données SQL Azure</li><li>Table Azure</li></ul> | Non | N/D

#### Exemples

**minimumSizeMB :**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Jeux de données externes

Les jeux de données externes sont ceux qui ne sont pas générés par un pipeline en cours d’exécution dans la fabrique de données. Si le jeu de données est marqué comme étant **external**, la stratégie **ExternalData** peut être définie pour influencer le comportement de la disponibilité du segment du jeu de données.

À moins qu’un jeu de données soit généré par Azure Data Factory, il doit être marqué comme étant **external**. Cela s’applique généralement aux entrées de la première activité dans un pipeline, à moins que l’activité ou le chaînage de pipeline soient utilisés.

| Nom | Description | Requis | Valeur par défaut |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Durée du délai de la vérification de la disponibilité des données externes pour le segment donné. Par exemple, si les données sont supposées être disponibles toutes les heures, la vérification permettant de déterminer si les données externes sont réellement disponibles et si le segment correspondant dispose de l’état Ready peut être différée selon la valeur de dataDelay.<br/><br/>S’applique uniquement à l’heure actuelle ; par exemple, s’il est 13 h et que cette valeur est de 10 minutes, la validation commence à 13 h 10.<br/><br/>Ce paramètre n’affecte pas les segments dans le passé (segments avec Slice End Time + dataDelay < maintenant) qui sont traités sans délai.<br/><br/>Au-delà de 23 heures et 59 minutes, vous devez utiliser le format jour.heures:minutes:secondes. Par exemple, pour spécifier 24 heures, n'utilisez pas 24:00:00 ; utilisez plutôt 1.00:00:00. Si vous utilisez 24:00:00, cette valeur sera traitée comme 24 jours (24.00:00:00). Pour 1 jour et 4 heures, spécifiez 1:04:00:00. | Non | 0 |
| retryInterval | Délai d'attente entre un échec et la nouvelle tentative. S'applique à l'heure actuelle ; si la tentative précédente a échoué, le système laisse ce délai s'écouler après la dernière tentative. <br/><br/>S’il est 13 h actuellement, la première tentative commence. Si la durée de la première vérification de validation est de 1 minute et si l'opération a échoué, la tentative suivante aura lieu à 13h + 1 min (durée) + 1 minute (intervalle avant nouvelle tentative) = 13h02. <br/><br/>Pour les segments dans le passé, il n’y a aucun délai. La nouvelle tentative a lieu immédiatement. | Non | 00:01:00 (1 minute) | 
| retryTimeout | Le délai d’attente pour chaque nouvelle tentative.<br/><br/>S’il est défini sur 10 minutes, la validation doit être effectuée en 10 minutes maximum. S’il faut plus de 10 minutes pour effectuer la validation, la nouvelle tentative expire.<br/><br/>Si toutes les tentatives de validation expirent, le segment est marqué comme TimedOut. | Non | 00:10:00 (10 minutes) |
| maximumRetry | Nombre de fois où la disponibilité des données externes est vérifiée. La valeur maximale autorisée est de 10. | Non | 3 | 

## Étendue des jeux de données
Vous pouvez créer des jeux de données étendues vers un pipeline à l’aide de la propriété **datasets**. Ces jeux de données peuvent uniquement être utilisés par les activités dans ce pipeline et non pas par les activités d’autres pipelines. L’exemple suivant définit un pipeline avec deux jeux de données à utiliser dans le pipeline : InputDataset-rdc et OutputDataset-rdc.

> [AZURE.IMPORTANT] Les étendues des jeux de données sont uniquement prises en charge avec les pipelines à usage unique (**pipelineMode** défini sur **OneTime**). Pour plus d’informations, consultez [Pipeline onetime](data-factory-scheduling-and-execution.md#onetime-pipeline).

	{
	    "name": "CopyPipeline-rdc",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset-rdc"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset-rdc"
	                    }
	                ],
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1,
	                    "style": "StartOfInterval"
	                },
	                "name": "CopyActivity-0"
	            }
	        ],
	        "start": "2016-02-28T00:00:00Z",
	        "end": "2016-02-28T00:00:00Z",
	        "isPaused": false,
	        "pipelineMode": "OneTime",
	        "expirationTime": "15.00:00:00",
	        "datasets": [
	            {
	                "name": "InputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "InputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/input",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": true,
	                    "policy": {}
	                }
	            },
	            {
	                "name": "OutputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "OutputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/output",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": false,
	                    "policy": {}
	                }
	            }
	        ]
	    }
	}

<!---HONumber=AcomDC_0504_2016-->