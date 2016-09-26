<properties 
	pageTitle="Créer et planifier des pipelines et des activités à la chaîne dans Data Factory | Microsoft Azure" 
	description="Apprenez à créer un pipeline de données dans Azure Data Factory pour déplacer et transformer des données. Créez un flux de travail piloté par les données pour produire des informations prêtes à l’emploi." 
    keywords="pipeline de données, flux de travail piloté par les données"
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Pipelines et activités dans Azure Data Factory : créer et planifier des pipelines et des activités à la chaîne
Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory, et à les utiliser dans l’optique de créer des workflows pilotés par les données de bout en bout pour votre scénario.

> [AZURE.NOTE] Cet article suppose que vous avez parcouru les articles [Présentation du service Azure Data Factory, un service d’intégration de données dans le cloud](data-factory-introduction.md) et [Jeux de données dans Azure Data Factory](data-factory-create-datasets.md) au préalable. Si vous n’avez pas d’expérience pratique de création de fabriques de données, le didacticiel [Créer votre première fabrique de données](data-factory-build-your-first-pipeline.md) vous aidera à mieux comprendre cet article.

## Qu’est-ce qu’un pipeline de données ?
Un **pipeline constitue un regroupement logique d'activités**. Ils sont utilisés pour regrouper des activités dans une unité qui exécute une tâche. Pour mieux comprendre les pipelines, vous devez d'abord comprendre une activité.

## Qu'est-ce qu'une activité ?
Les activités définissent les actions à effectuer sur les données. Chaque activité accepte ou non des [jeux de données](data-factory-create-datasets.md) en tant qu'entrées et produit au moins un jeu de données en tant que sortie. **Une activité est une unité d'orchestration dans Azure Data Factory.**

Par exemple, vous pouvez utiliser une activité de copie pour orchestrer la copie de données d'un jeu de données vers un autre. De même, vous pouvez utiliser une activité HDInsight Hive pour exécuter une requête Hive sur un cluster Azure HDInsight afin de transformer ou d’analyser vos données. Azure Data Factory fournit un large éventail d'activités de [transformation, d'analyse](data-factory-data-transformation-activities.md) et de [déplacement de données](data-factory-data-movement-activities.md). Vous pouvez également choisir de créer une activité .NET personnalisée pour exécuter votre propre code.

Examinez les deux jeux de données suivants :

**Jeu de données SQL Azure**

La table « MyTable » contient une colonne « timestampcolumn » qui permet de spécifier la valeur date-heure d’insertion des données dans la base de données.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Jeu de données Blob Azure**

Les données sont copiées vers un nouvel objet blob toutes les heures. Le chemin d’accès de l’objet blob reflète la date-heure spécifique avec une granularité horaire.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


L’activité Copie dans le pipeline suivant copie les données à partir de SQL Azure vers le stockage blob Azure. Elle considère la table SQL Azure en tant que jeu de données d'entrée selon une fréquence horaire et écrit les données dans le stockage Blob Azure représenté par le jeu de données « AzureBlobOutput ». Le jeu de données de sortie possède également une fréquence horaire. Reportez-vous à la [section de planification et d’exécution](#scheduling-and-execution) pour comprendre comment les données sont copiées selon l’unité de temps. Ce pipeline a une période active de trois heures de « 2015-01-01T08:00:00 » à « 2015-01-01T11:00:00 ».

**Pipeline :**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

Maintenant que nous comprenons en quoi consiste une activité, revenons au pipeline.
 
Un pipeline constitue un regroupement logique d'activités. Ils sont utilisés pour regrouper des activités dans une unité qui exécute une tâche. **Un pipeline est également l'unité de déploiement et de gestion des activités.** Par exemple, vous pouvez souhaiter regrouper dans un seul pipeline des activités liées logiquement afin qu'elles puissent se trouver dans l'état actif ou en pause simultanément.

Un jeu de données de sortie d'une activité dans un pipeline peut être le jeu de données d'entrée d'une autre activité dans le même pipeline ou un pipeline différent en définissant des dépendances entre les activités. Consultez [Planification et exécution](#chaining-activities) pour plus d’informations.

Les étapes types lors de la création d'un pipeline dans Azure Data Factory sont les suivantes :

1.	Créer une fabrique de données (si nécessaire).
2.	Créer un service lié pour chaque magasin de données ou calcul.
3.	Créer des jeux de données d'entrée et de sortie.
4.	Créer un pipeline avec des activités qui opèrent sur les jeux de données.

![Entités Data Factory](./media/data-factory-create-pipelines/entities.png)

Examinons de plus près la définition d'un pipeline.

## Anatomie d'un pipeline  

La structure générique d'un pipeline se présente comme suit :

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

La section **Activités** peut contenir une ou plusieurs activités définies. Chaque activité possède la structure de niveau supérieur suivante :

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

Le tableau suivant décrit les propriétés dans les définitions JSON du pipeline et de l'activité :

Tag | Description | Requis
--- | ----------- | --------
name | Nom de l'activité ou du pipeline. Spécifiez un nom qui représente l’action que l’activité ou le pipeline doit exécuter<br/><ul><li>Nombre maximal de caractères : 260</li><li>Doit commencer par une lettre, un nombre ou un trait de soulignement (\_)</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \\ »</li></ul> | Oui
description | Texte décrivant la raison motivant l’activité ou le pipeline. | Oui
type | Spécifie le type de l'activité. Consultez les articles [Activités de déplacement des données](data-factory-data-movement-activities.md) et [Activités de transformation des données](data-factory-data-transformation-activities.md) pour en savoir plus sur les différents types d’activités. | Oui
inputs | Les tables d’entrée utilisées par l’activité<br/><br/>// une table d’entrée<br/>"inputs": [ { "name": "inputtable1" } ],<br/><br/>// deux tables d’entrée <br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ], | Oui
outputs | Les tables de sortie utilisées par l’activité.// une table de sortie<br/>"outputs": [ { "name": "outputtable1" } ],<br/><br/>// deux tables de sortie<br/>"outputs": [ { "name": "outputtable1" }, { "name": "outputtable2" } ], | Oui
linkedServiceName | Nom du service lié utilisé par l’activité. <br/><br/>Une activité peut nécessiter que vous spécifiiez le service lié à l’environnement de calcul requis. | Oui pour les activités HDInsight et de calcul de score Azure Machine Learning <br/><br/>Non pour toutes les autres
typeProperties | Les propriétés de la section typeProperties dépendent du type de l’activité. | Non
policy | Stratégies affectant le comportement d’exécution de l’activité. Si aucune valeur n’est spécifiée, les stratégies par défaut sont utilisées. | Non
start | Date et heure de début du pipeline. Doit se trouver au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. <br/><br/>Il est possible de spécifier une heure locale, par exemple une heure EST. Voici un exemple : « 2016-02-27T06:00:00**-05:00** », qui équivaut à 6 h EST.<br/><br/>Ensemble, les propriétés de début et de fin spécifient la période active du pipeline. Les tranches de sortie sont uniquement générées pendant cette période active. | Non<br/><br/>Si vous spécifiez une valeur pour la propriété end, vous devez en spécifier une pour la propriété start.<br/><br/>Les heures de début et de fin peuvent être toutes les deux non renseignées pour créer un pipeline. Elles doivent avoir une valeur pour définir une période active d’exécution du pipeline. Si vous ne spécifiez pas d’heures de début et de fin lors de la création d’un pipeline, vous pouvez les définir à l’aide de l’applet de commande Set-AzureRmDataFactoryPipelineActivePeriod ultérieurement.
end | Date et heure de fin du pipeline. Si spécifiée, doit être au format ISO. Par exemple : 2014-10-14T17:32:41Z <br/><br/>Il est possible de spécifier une heure locale, par exemple une heure EST. Voici un exemple : « 2016-02-27T06:00:00**-05:00** », qui équivaut à 6 h EST.<br/><br/>Pour exécuter le pipeline indéfiniment, spécifiez 9999-09-09 comme valeur pour la propriété end. | Non <br/><br/>Si vous spécifiez une valeur pour la propriété start, vous devez en spécifier une pour la propriété end.<br/><br/>Consultez les remarques relatives à la propriété **start**.
isPaused | Si la valeur est true, le pipeline ne s’exécute pas. Valeur par défaut = false. Vous pouvez utiliser cette propriété pour activer ou désactiver. | Non 
scheduler | La propriété « scheduler » est utilisée pour définir la planification souhaitée pour l’activité. Ses sous-propriétés sont les mêmes que celles de la [propriété de disponibilité dans un jeu de données](data-factory-create-datasets.md#Availability). | Non |   
| pipelineMode | La méthode de planification des exécutions pour le pipeline. Les valeurs autorisées sont : scheduled (par défaut) et onetime.<br/><br/>« scheduled » indique que le pipeline s’exécute selon un intervalle de temps spécifié en fonction de sa période active (heure de début et de fin). « Onetime » indique que le pipeline ne s’exécute qu’une seule fois. Une fois créés, les pipelines de type onetime ne peuvent pas être modifiés ni mis à jour pour le moment. Consultez la page [Pipeline onetime](data-factory-scheduling-and-execution.md#onetime-pipeline) pour plus d’informations sur le paramètre onetime. | Non | 
| expirationTime | Durée pendant laquelle le pipeline est valide et doit rester configuré. Le pipeline est automatiquement supprimé une fois le délai d’expiration atteint s’il ne contient aucune exécution active, en échec ou en attente. | Non | 
| datasets | Liste des jeux de données à utiliser par les activités définies dans le pipeline. Cette propriété permet de définir des jeux de données spécifiques à ce pipeline et non définis dans la fabrique de données. Les jeux de données définis dans ce pipeline ne peuvent être utilisés que par ce pipeline et ne peuvent pas être partagés. Pour plus d’informations, consultez la page [Étendue des jeux de données](data-factory-create-datasets.md#scoped-datasets).| Non |  
 

## Types d’activités pour le déplacement et la transformation des données.
Azure Data Factory fournit un large éventail d’activités de [déplacement des données](data-factory-data-movement-activities.md) et de [transformation des données](data-factory-data-transformation-activities.md).

### Stratégies
Les stratégies affectent le comportement d'exécution d'une activité, en particulier lors du traitement du segment d'une table. Le tableau suivant fournit les détails.

Propriété | Valeurs autorisées | Valeur par défaut | Description
-------- | ----------- | -------------- | ---------------
accès concurrentiel | Entier <br/><br/>Valeur max : 10 | 1 | Nombre d’exécutions simultanées de l’activité.<br/><br/>Il détermine le nombre d’exécutions en parallèle de l’activité qui peuvent se produire sur différents segments. Par exemple, si une activité doit passer par un grand ensemble de données disponibles, une valeur de concurrence plus élevée accélère le traitement des données. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Détermine l’ordre des segments de données qui sont traités.<br/><br/>Par exemple, si vous avez 2 segments (l’un se produisant à 16 heures et l’autre à 17 heures) et que les deux sont en attente d’exécution. Si vous définissez executionPriorityOrder sur NewestFirst, le segment à 17 h est traité en premier. De même, si vous définissez executionPriorityOrder sur OldestFIrst, le segment à 16 h est traité en premier. 
retry | Entier<br/><br/>La valeur max peut être 10 | 3 | Nombre de nouvelles tentatives avant que le traitement des données du segment ne soit marqué comme un échec. L'exécution de l'activité pour un segment de données est répétée jusqu'au nombre de tentatives spécifié. La nouvelle tentative est effectuée dès que possible après l'échec.
timeout | TimeSpan | 00:00:00 | Délai d'expiration de l'activité. Exemple : 00:10:00 (implique un délai d’expiration de 10 minutes)<br/><br/>Si une valeur n’est pas spécifiée ou est égale à 0, le délai d’expiration est infini.<br/><br/>Si le temps de traitement des données sur un segment dépasse la valeur du délai d’expiration, il est annulé et le système tente de réexécuter le traitement. Le nombre de nouvelles tentatives dépend de la propriété « retry ». Quand le délai d’expiration est atteint, l’état est défini sur TimedOut.
delay | TimeSpan | 00:00:00 | Spécifie le délai avant le début du traitement des données du segment.<br/><br/>L’exécution de l’activité pour un segment de données est démarrée une fois que le Délai est au-delà de la durée d’exécution prévue.<br/><br/>Exemple : 00:10:00 (implique un délai de 10 minutes)
longRetry | Entier<br/><br/>Valeur max : 10 | 1 | Le nombre de nouvelles tentatives longues avant l’échec de l’exécution du segment.<br/><br/>Les tentatives longRetry sont espacées par longRetryInterval. Par conséquent, si vous devez spécifier un délai entre chaque tentative, utilisez longRetry. Si les valeurs Retry et longRetry sont spécifiées, chaque tentative longRetry inclut des tentatives Retry et le nombre maximal de tentatives sera égal à Retry * longRetry.<br/><br/>Par exemple, si la stratégie de l’activité inclut :<br/>Retry : 3<br/>longRetry : 2<br/>longRetryInterval : 01:00:00<br/><br/>Supposons qu’il existe un seul segment à exécuter (dont l’état est Waiting) et que l’exécution de l’activité échoue à chaque fois. Au départ, il y aurait 3 tentatives consécutives d'exécution. Après chaque tentative, l’état du segment serait Retry. Une fois les 3 premières tentatives terminées, l’état du segment serait LongRetry.<br/><br/>Après une heure (c’est-à-dire la valeur de longRetryInterval), il y aurait un autre ensemble de 3 tentatives consécutives d’exécution. Ensuite, l'état du segment serait Failed et aucune autre tentative ne serait exécutée. Par conséquent, 6 tentatives ont été exécutées.<br/><br/>Si une exécution réussit, l’état du segment est défini sur Ready et aucune nouvelle tentative n’est exécutée.<br/><br/>La valeur longRetry peut être utilisée dans les situations où les données dépendantes arrivent à des moments non déterministes ou lorsque l’environnement global où le traitement des données se produit est douteux. Dans ces cas, l’exécution de nouvelles tentatives l’une après l’autre peut ne pas être utile et procéder ainsi après un intervalle de temps précis produit la sortie désirée.<br/><br/>Mise en garde : ne définissez pas de valeurs élevées pour longRetry ou longRetryInterval. En règle générale, des valeurs plus élevées impliquent d’autres problèmes systémiques. 
longRetryInterval | TimeSpan | 00:00:00 | Le délai entre les nouvelles tentatives longues 

## Chaînage des activités
Si vous avez plusieurs activités dans un pipeline et que la sortie d’une activité n’est pas une entrée dans une autre activité, les activités peuvent s’exécuter en parallèle si les segments de données d’entrée pour les activités sont prêts.

Vous pouvez chaîner deux activités en utilisant le jeu de données de sortie d’une activité en tant que jeu de données d’entrée de l’autre activité. Les activités peuvent être dans le même pipeline ou dans des pipelines différents. La seconde activité s’exécute uniquement quand la première se termine correctement.

Considérez l’exemple suivant :
 
1.	Le pipeline P1 contient l’activité A1 nécessitant le jeu de données d’entrée externe D1 et produit le jeu de données de **sortie** **D2**.
2.	Le pipeline P2 contient l’activité A2 nécessitant le jeu de données **d’entrée** **D2** et produit le jeu de données de sortie D3.
 
Dans ce scénario, l’activité A1 s’exécute lorsque les données externes seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. L’activité A2 s’exécute lorsque les tranches planifiées de D2 seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. S’il existe une erreur dans l’une des tranches du jeu de données D2, A2 n’est pas exécutée pour cette tranche jusqu’à ce que celle-ci devienne disponible.

Vue schématique :

![Chaînage des activités dans deux pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Vue schématique avec les deux activités dans le même pipeline :

![Chaînage des activités dans le même pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

## Planification et exécution
Vous savez désormais en quoi consistent les pipelines et les activités. Vous avez également observé comment ils sont définis et vous avez obtenu un aperçu des activités dans Azure Data Factory. À présent, examinons leur exécution.

Un pipeline est actif uniquement entre son heure de début et son heure de fin. Il n'est pas exécuté avant l'heure de début, ni après l'heure de fin. Lorsque le pipeline est suspendu, il n’est pas exécuté, quelle que soit son heure de début et de fin. Pour qu'un pipeline soit exécuté, il ne doit pas être suspendu. En fait, ce n'est pas le pipeline qui est exécuté. Ce sont les activités dans le pipeline qui sont exécutées. Toutefois, cela se produit dans le contexte global du pipeline.

Consultez [Planification et exécution](data-factory-scheduling-and-execution.md) pour comprendre le fonctionnement de planification et de l’exécution dans Azure Data Factory.

### Traitement en parallèle des tranches
Définissez **concurrency** dans la définition JSON de l’activité sur une valeur supérieure à 1, afin que plusieurs segments soient traités en parallèle par plusieurs instances de l’activité au moment de l’exécution. Cette fonctionnalité est très utile lors du traitement de segments précédents.


## Création et gestion d'un pipeline
Azure Data Factory fournit plusieurs mécanismes pour créer et déployer des pipelines (qui à leur tour contiennent une ou plusieurs activités).

### En passant par le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Accédez à votre instance Azure Data Factory dans laquelle vous souhaitez créer un pipeline.
3. Cliquez sur la vignette **Créer et déployer** dans le filtre **Résumé**.
 
	![Vignette Créer et déployer](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Cliquez sur **Nouveau pipeline** dans la barre de commandes.

	![Bouton Nouveau pipeline](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. La fenêtre de l'éditeur avec le modèle JSON du pipeline doit s'afficher.

	![Éditeur de pipeline](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Une fois que vous avez terminé la création du pipeline, cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.

	> [AZURE.NOTE] Pendant le déploiement, le service Azure Data Factory effectue quelques vérifications de validation pour aider à résoudre les problèmes courants. S’il existe une erreur, les informations correspondantes s’affichent. Effectuez des actions correctives et redéployez le pipeline créé. Vous pouvez utiliser l'éditeur pour mettre à jour et supprimer un pipeline.

Pour connaître la procédure de bout en bout de création d’une fabrique de données avec un pipeline, voir [Créer votre première fabrique de données Azure à l’aide du portail Azure et de Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md).

### Utilisation du plug-in Visual Studio
Vous pouvez utiliser Visual Studio pour créer et déployer des pipelines dans Azure Data Factory. Pour connaître la procédure de bout en bout de création d’une fabrique de données avec un pipeline, voir [Créer votre première fabrique de données Azure à l’aide de Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md).


### Utilisation de Microsoft Azure PowerShell
Vous pouvez utiliser Azure PowerShell pour créer des pipelines dans Azure Data Factory. Par exemple, vous avez défini le pipeline JSON dans un fichier sous c:\\DPWikisample.json. Vous pouvez le télécharger dans votre instance Azure Data Factory comme indiqué dans l'exemple suivant.

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Pour connaître la procédure de bout en bout de création d’une fabrique de données avec un pipeline, voir [Créer votre première fabrique de données Azure à l’aide d’Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md).

### Utilisation du kit de développement logiciel (SDK) .NET
Vous pouvez aussi créer et déployer le pipeline avec le kit de développement logiciel (SDK) .NET. Ce mécanisme peut être utilisé pour créer des pipelines par programme. Voir [Créer, surveiller et gérer des fabriques de données Azure à l’aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically.md) pour plus d’informations.


### Utilisation d’un modèle Azure Resource Manager
Vous pouvez créer et déployer le pipeline à l’aide d’un modèle Azure Resource Manager. Voir [Didacticiel : concevoir votre première fabrique de données Azure à l’aide du modèle Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) pour plus d’informations.

### Utilisation de l'API REST
Vous pouvez également créer et déployer le pipeline avec les API REST. Ce mécanisme peut être utilisé pour créer des pipelines par programme. Voir [Création ou mise à jour de pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx) pour plus d’informations.


## Gérer et surveiller  
Une fois qu’un pipeline est déployé, vous pouvez gérer et surveiller vos pipelines, segments et exécutions. Pour plus d’informations, consultez l’article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

## Étapes suivantes

- Comprendre [la planification et l’exécution dans Azure Data Factory](data-factory-scheduling-and-execution.md).
- En savoir plus sur le [déplacement des données](data-factory-data-movement-activities.md) et les [fonctionnalités de transformation des données](data-factory-data-transformation-activities.md) dans Azure Data Factory.
- Comprendre [la gestion et la surveillance dans Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Générer et déployer votre premier pipeline](data-factory-build-your-first-pipeline.md).

<!---HONumber=AcomDC_0914_2016-->