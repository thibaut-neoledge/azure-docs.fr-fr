<properties 
	pageTitle="Création de pipelines" 
	description="Comprenez les pipelines dans Azure Data Factory et découvrez comment les créer pour déplacer et transformer des données afin de produire des informations qui peuvent être exploitées." 
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
	ms.topic="article" y
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Présentation des pipelines et des activités
Cet article vous aidera à comprendre les pipelines et les activités dans Azure Data Factory et comment les utiliser pour créer des flux de travail pilotés par les données de bout en bout pour votre scénario ou votre entreprise. Cet article suppose que vous avez parcouru les articles [Présentation](data-factory-introduction.md) et [Création de jeux de données](data-factory-create-datasets.md) au préalable.

## Qu'est-ce qu'un pipeline ?
Un **pipeline constitue un regroupement logique d'activités**. Ils sont utilisés pour regrouper des activités dans une unité qui exécute une tâche. Pour mieux comprendre les pipelines, vous devez d'abord comprendre une activité.

### Qu'est-ce qu'une activité ?
Les activités définissent les actions à effectuer sur les données. Chaque activité accepte ou non des [jeux de données](data-factory-create-datasets.md) en tant qu'entrées et produit au moins un jeu de données en tant que sortie. **Une activité est une unité d'orchestration dans Azure Data Factory.**

Par exemple, vous pouvez utiliser une activité de copie pour orchestrer la copie de données d'un jeu de données vers un autre. De même, vous pouvez utiliser une activité Hive qui exécutera une requête Hive sur un cluster Azure  HDInsight afin de transformer ou d’analyser vos données. Azure Data Factory fournit un large éventail d'activités de [transformation, d'analyse](data-factory-data-transformation-activities.md) et de [déplacement de données](data-factory-data-movement-activities.md). Vous pouvez également choisir de créer une activité .NET personnalisée pour exécuter votre propre code.

Examinons les 2 jeux de données suivants :

**Jeu de données SQL Azure**

La table « MyTable » contient une colonne « timestampcolumn » qui permet de spécifier la valeur date-heure d'insertion des données dans la base de données.

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

Les données sont copiées vers un nouvel objet blob toutes les heures. Le chemin d'accès à l'objet blob reflète la valeur date-heure spécifique avec une granularité horaire.

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


L'activité Copie dans le pipeline ci-dessous copie les données à partir de SQL Azure vers le stockage Blob Azure. Elle considère la table SQL Azure en tant que jeu de données d'entrée selon une fréquence horaire et écrit les données dans le stockage Blob Azure représenté par le jeu de données « AzureBlobOutput ». Le jeu de données de sortie possède également une fréquence horaire. Reportez-vous à la section de planification et d'exécution pour comprendre comment les données sont copiées selon l'unité de temps. Ce pipeline a une période active de 3 heures de « 2015-01-01T08:00:00 » à « 2015-01-01T11:00:00 ».

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

Un jeu de données de sortie d'une activité dans un pipeline peut être le jeu de données d'entrée d'une autre activité dans le même pipeline ou un pipeline différent en définissant des dépendances entre les activités. La section [Planification et exécution](#scheduling-and-execution) explique ceci en détail.

Les étapes types lors de la création d'un pipeline dans Azure Data Factory sont les suivantes :

1.	Créer une fabrique de données (si nécessaire).
2.	Créer un service lié pour chaque magasin de données ou calcul.
3.	Créer des jeux de données d'entrée et de sortie.
4.	Créer un pipeline avec des activités qui opèrent sur les jeux de données définis ci-dessus.

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

La section **Activités** peut contenir une ou plusieurs activités définies. Chaque activité possède la structure de niveau supérieure suivante :

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

Balise | Description | Requis
--- | ----------- | --------
name | Nom de l'activité ou du pipeline. Spécifiez un nom qui représente l'action que l'activité ou le pipeline doit exécuter<br/><ul><li>Nombre maximal de caractères : 260</li><li>Doit commencer par une lettre, un nombre ou un trait de soulignement (_)</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « \* », « % », « & », « : », « \\ »</li></ul>|Oui
Description | Texte décrivant l’utilisation de l'activité ou du pipeline | Oui
Type | Spécifie le type de l'activité. Consultez les articles [Activités de déplacement des données](data-factory-data-movement-activities.md) et [Activités de transformation des données](data-factory-data-transformation-activities.md) pour différents types d'activités. | Oui
Entrées | Tables d'entrées utilisées par l'activité<p>// une table d'entrée<br/>« entrées » : [ { « nom » : « inputtable1 » } ],</p><p>// deux tables d'entrée<br/>« entrées » : [ { « nom » : « inputtable1 » }, { « nom » : « inputtable2 » } ],</p> | Oui
Sorties | Tables de sortie utilisées par l'activité.<p>// une table de sortie<br/>« sorties » : [ { « nom » : « outputtable1 » } ],</p><p>//deux tables de sortie<br/>« sorties » : [ { « nom » : « outputtable1 » }, { « nom » : « outputtable2 » } ],</p> | Oui
linkedServiceName | Nom du service lié utilisé par l'activité. <p>Une activité peut nécessiter que vous indiquiez le service lié qui effectue la liaison avec l'environnement de calcul requis.</p> | Oui pour Activité de HDInsight et Activité de notation par lot Azure Machine Learning<p>Non pour toutes les autres</p>
typeProperties | Les propriétés de la section typeProperties dépendent du type de l'activité. Consultez l'article sur chaque activité pour en savoir plus | Aucune
stratégie | Stratégies qui affectent le comportement d'exécution de l'activité. Si aucune valeur n'est spécifiée, les stratégies par défaut seront utilisées. Faites défiler vers le bas pour plus d'informations | Aucun
démarrage | Date-heure de début pour le pipeline. Doit se trouver au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. Ensemble, les propriétés de début et de fin spécifient la période active du pipeline. Les segments de sortie sont uniquement générés dans cette période active. | Non <p>La période active d'un pipeline peut également être définie à l'aide de l'applet de commande Set-AzureDataFactoryPipelineActivePeriod</p>
Fin | Date-heure de fin pour le pipeline. Si spécifiée, doit être au format ISO. Par exemple : 2014-10-14T17:32:41Z<p>Si elle n'est pas spécifiée, elle est calculée comme le « début + 48 heures ». Pour exécuter le pipeline indéfiniment, spécifiez 9999-09-09 comme valeur pour la propriété de fin.</p>| Non
isPaused | Si la valeur est définie sur true, le pipeline n'est pas exécuté. Valeur par défaut = false. Vous pouvez utiliser cette propriété pour activer ou désactiver. | Aucun
planificateur | La propriété « planificateur » est utilisée pour définir la planification souhaitée pour l'activité. Les sous-propriétés sont les mêmes que celles de la [propriété de disponibilité dans un jeu de données](data-factory-create-datasets.md#Availability). | Non   | 

### Types d'activité
Azure Data Factory fournit un large éventail d'activités de [déplacement des données](data-factory-data-movement-activities.md) et de [transformation des données](data-factory-data-transformation-activities.md).

### Stratégies
Les stratégies affectent le comportement d'exécution d'une activité, en particulier lors du traitement du segment d'une table. Le tableau suivant fournit les détails.

Propriété | Valeurs autorisées | Valeur par défaut | Description
-------- | ----------- | -------------- | ---------------
accès concurrentiel | Entier <p>Valeur max : 10</p> | 1 | Nombre d'exécutions simultanées de l'activité.<p>Il détermine le nombre d'exécutions en parallèle de l'activité qui peuvent se produire sur différents segments. Par exemple, si une activité doit passer par un grand ensemble de données disponibles, une concurrence plus élevée accélère le traitement des données.</p> 
executionPriorityOrder | NewestFirst<p>OldestFirst</p> | OldestFirst | Détermine l'ordre des segments de données qui sont traités.<p>Par exemple, si vous avez 2 segments (l'un se produisant à 16h et l'autre à 17h) et que les deux sont en attente d'exécution. Si vous définissez executionPriorityOrder sur NewestFirst, le segment à 17h est traité en premier. De même, si vous définissez executionPriorityOrder sur OldestFIrst, le segment à 16h est traité en premier.</p> 
retry | Entier<p>Valeur max peut être 10</p> | 3 | Nombre de nouvelles tentatives avant que le traitement des données du segment ne soit marqué comme un échec. L'exécution de l'activité pour un segment de données est répétée jusqu'au nombre de tentatives spécifié. La nouvelle tentative est effectuée dès que possible après l'échec.
timeout | TimeSpan | 00:00:00 | Délai d'expiration de l'activité. Exemple : 00:10:00 (implique un délai d'expiration de 10 minutes)<p>Si une valeur n'est pas spécifiée ou est égale à 0, le délai d'expiration est infini.</p><p>Si le temps de traitement des données sur un segment dépasse la valeur du délai d'expiration, il est annulé et le système tente de réexécuter le traitement. Le nombre de nouvelles tentatives dépend de la propriété « retry ». Lorsque le délai d'expiration est atteint, l'état est défini sur TimedOut.</p>
delay | TimeSpan | 00:00:00 | Spécifiez le délai avant le début du traitement des données du segment.<p>L'exécution de l'activité pour un segment de données est démarré une fois que le Délai est au-delà de la durée d'exécution prévue.</p><p>Exemple : 00:10:00 (implique un délai de 10 minutes)</p>
longRetry | Entier<p>Valeur max : 10</p> | 1 | Le nombre de nouvelles tentatives longues avant l'échec de l'exécution du segment.<p>Les tentatives longRetry sont espacées par longRetryInterval. Par conséquent, si vous devez spécifier un délai entre chaque tentative, utilisez longRetry. Si les valeurs Retry et longRetry sont spécifiées, chaque tentative longRetry inclut des tentatives Retry et le nombre maximal de tentatives sera égal à Retry * longRetry.</p><p>Par exemple, si la stratégie de l'activité inclut :<br/>Retry : 3<br/>longRetry : 2<br/>longRetryInterval : 01:00:00<br/></p><p>Supposons qu'il existe un seul segment à exécuter (dont l'état est PendingExecution) et que l'exécution de l'activité échoue à chaque fois. Au départ, il y aurait 3 tentatives consécutives d'exécution. Après chaque tentative, l'état du segment serait Retry. Une fois les 3 premières tentatives terminées, l'état du segment serait LongRetry.</p><p>Après une heure (c'est-à-dire la valeur de longRetryInteval), il y aurait un autre ensemble de 3 tentatives consécutives d'exécution. Ensuite, l'état du segment serait Failed et aucune autre tentative ne serait exécutée. Par conséquent, 6 tentatives ont été exécutées.</p><p>Remarque : si une exécution réussit, l'état du segment est défini sur Ready et aucune nouvelle tentative n'est exécutée.</p><p>La valeur longRetry peut être utilisée dans les situations où les données dépendantes arrivent à des moments non déterministes ou lorsque l'environnement global où le traitement des données se produit est relativement douteux. Dans ces cas, l'exécution de nouvelles tentatives l'une après l'autre peut ne pas être utile et procéder ainsi après un intervalle de temps précis produit la sortie désirée.</p><p>Mise en garde : ne définissez pas de valeurs élevées pour longRetry ou longRetryInterval. En général, les valeurs élevées entraînent d'autres problèmes systémiques</p> 
longRetryInterval | TimeSpan | 00:00:00 | Le délai entre les nouvelles tentatives longues 

## Création et gestion d'un pipeline
Azure Data Factory fournit plusieurs mécanismes pour créer et déployer des pipelines (qui à leur tour contiennent une ou plusieurs activités).

### Utilisation du portail Azure en version préliminaire

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com/).
2. Accédez à votre instance Azure Data Factory dans laquelle vous souhaitez créer un pipeline.
3. Cliquez sur la vignette **Créer et déployer** dans le filtre **Résumé**. 
 
	![Vignette Créer et déployer](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Cliquez sur **Nouveau pipeline** dans la barre de commandes.

	![Bouton Nouveau pipeline](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. La fenêtre de l'éditeur avec le modèle JSON du pipeline doit s'afficher.

	![Éditeur de pipeline](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Une fois que vous avez terminé la création du pipeline, cliquez sur **Déployer** sur la barre de commandes pour déployer le pipeline.

	**Remarque :** pendant le déploiement, le service Azure Data Factory effectue quelques vérifications de validation pour aider à résoudre les problèmes courants. S'il existe une erreur, les informations correspondantes s'affichent. Effectuez des actions correctives et redéployez le pipeline créé. Vous pouvez utiliser l'éditeur pour mettre à jour et supprimer un pipeline.

### Utilisation du plug-in Visual Studio
Vous pouvez utiliser Visual Studio pour créer et déployer des pipelines dans Azure Data Factory. Pour plus d'informations, consultez [Didacticiel : copier des données depuis le stockage Azure vers Azure SQL (Visual Studio)](data-factory-get-started-using-vs.md).

### Utilisation de Microsoft Azure PowerShell
Vous pouvez utiliser Azure PowerShell pour créer des pipelines dans Azure Data Factory. Par exemple, vous avez défini le pipeline JSON dans un fichier sous c:\\DPWikisample.json. Vous pouvez le télécharger dans votre instance Azure Data Factory comme indiqué dans l'exemple suivant.

	New-AzureDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Pour en savoir plus sur cette applet de commande, consultez [Applet de commande New-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn820227.aspx).

### Utilisation de l'API REST
Vous pouvez également créer et déployer le pipeline avec les API REST. Ce mécanisme peut être utilisé pour créer des pipelines par programme. Pour en savoir plus, consultez [Créer ou mettre à jour un pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx).

### Utilisation du kit de développement logiciel (SDK) .NET
Vous pouvez aussi créer et déployer le pipeline avec le kit de développement logiciel (SDK) .NET. Ce mécanisme peut être utilisé pour créer des pipelines par programme. Pour en savoir plus, consultez [Créer, gérer et surveiller les fabriques de données par programme](data-factory-create-data-factories-programmatically.md).


## Planification et exécution
Vous savez désormais en quoi consistent les pipelines et les activités. Vous avez également observé comment ils sont définis et vous avez obtenu un aperçu des activités dans Azure Data Factory. À présent, examinons leur exécution.

Un pipeline est actif uniquement entre son heure de début et son heure de fin. Il n'est pas exécuté avant l'heure de début, ni après l'heure de fin. Lorsque le pipeline est suspendu, il n'est pas exécuté, quelle que soit son heure de début et de fin. Pour qu'un pipeline soit exécuté, il ne doit pas être suspendu.

En fait, ce n'est pas le pipeline qui est exécuté. Ce sont les activités dans le pipeline qui sont exécutées. Toutefois, cela se produit dans le contexte global du pipeline. Consultez [Planification et exécution](data-factory-scheduling-and-execution.md) pour comprendre le fonctionnement de planification et de l'exécution dans Azure Data Factory.

## Gérer et surveiller  
Une fois qu'un pipeline est déployé, vous pouvez gérer et surveiller les pipelines, les segments et les exécutions. Pour plus d’informations, voir l’article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

## Étapes suivantes

- Comprendre [la planification et l'exécution dans Azure Data Factory](data-factory-scheduling-and-execution.md).  
- En savoir plus sur le [déplacement des données](data-factory-data-movement-activities.md) et les [fonctionnalités de transformation des données](data-factory-data-transformation-activities.md) dans Azure Data Factory.
- Comprendre [la gestion et la surveillance dans Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Générer et déployer votre premier pipeline](data-factory-build-your-first-pipeline.md). 

## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-create-pipelines.md).
 

   













 
 


 

 

<!---HONumber=Oct15_HO4-->