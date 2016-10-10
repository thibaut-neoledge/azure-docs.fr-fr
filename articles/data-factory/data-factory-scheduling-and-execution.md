<properties
	pageTitle="Planification et exécution avec Data Factory | Microsoft Azure"
	description="Apprenez à connaître les aspects de planification et d’exécution du modèle d’application Azure Data Factory."
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
	ms.date="08/22/2016"
	ms.author="spelluru"/>

# Planification et exécution avec Data Factory
Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory.

## Composants requis
Cet article suppose que vous avez des notions de base sur les concepts de modèle de données Data Factory, dont l’activité, les pipelines, les services connexes et les groupes de données. Pour les concepts de base d’Azure Data Factory, consultez les articles suivants :

- [Présentation de Data Factory](data-factory-introduction.md)
- [Pipelines](data-factory-create-pipelines.md)
- [Groupes de données](data-factory-create-datasets.md)

## Planification d’une activité

Grâce à la section planificateur de l’activité JSON, vous pouvez planifier l’activité pour qu’elle s’exécute de façon récurrente. Par exemple, vous pouvez planifier une activité toutes les heures comme suit :

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  

![Exemple de planificateur](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Comme indiqué dans le diagramme, la spécification d’un calendrier pour l'activité crée une série de fenêtres récurrentes. Les fenêtres récurrentes sont une série d’intervalles de temps fixes contigus, qui ne se chevauchent pas. Ces fenêtres récurrentes logiques pour l'activité sont appelés *fenêtres d'activité*.

Pour la fenêtre d’activité en cours d’exécution, vous pouvez accéder à l’intervalle de temps associé à la fenêtre d’activité par le biais des variables système [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) et [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) de l’activité JSON. Vous pouvez utiliser ces variables à différentes fins dans votre activité JSON. Par exemple, vous pouvez les utiliser pour sélectionner les données à partir des jeux de données d’entrée et de sortie représentant les données de série chronologique.

La propriété **scheduler** prend en charge les mêmes sous-propriétés que la propriété **availability** dans un jeu de données. Consultez [Disponibilité du jeu de données](data-factory-create-datasets.md#Availability) pour plus de détails. Exemples : planification à un décalage spécifique, définition du mode pour faire coïncider le traitement au début ou à la fin de l’intervalle de la fenêtre d’activité.

Vous pouvez spécifier les propriétés du **planificateur** pour une activité, mais cette propriété est **facultative**. Si vous définissez une propriété, elles devront correspondre à la cadence que vous spécifiez dans la définition du jeu de données de sortie. À ce stade, le jeu de données de sortie est ce qui pilote la planification : vous devez donc créer un jeu de données de sortie même si l’activité ne génère aucune sortie. Si l’activité ne prend aucune entrée, vous pouvez ignorer la création du jeu de données d’entrée.

## Jeux de données et tranches de données de série chronologique

Les données de série chronologique sont une séquence continue de points de données comprenant généralement des mesures successives effectuées pendant un certain intervalle de temps. Parmi les exemples de données de série chronologique, on trouve : des données de capteur, des données pour application de télémétrie.

Avec Data Factory, vous pouvez traiter les données de série par lot pendant l’exécution de l’activité. En règle générale, il existe des cadences périodiques régissant le rythme auquel les données d’entrée arrivent et les données de sortie sont générées. Cette cadence est modélisée en spécifiant la **Disponibilité** du jeu de données comme suit :

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Chaque unité de données consommée et produite pendant l’exécution d’une activité est appelée tranche de données. Le diagramme suivant illustre un exemple d’une activité avec un jeu de données d’entrée et un jeu de données de sortie. Ces jeux de données ont la propriété **Disponibilité** définie sur une fréquence de «Toutes les heures ».

![Planificateur de disponibilité](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Les tranches de données recueillies toutes les heures pour le jeu de données d’entrée et de sortie sont affichées dans le diagramme précédent. Le diagramme illustre trois tranches d’entrée qui sont prêtes pour le traitement. L’activité de 10 à 11 h est en cours, et produit la tranche de sortie de 10 à 11 h.

Vous pouvez accéder à l’intervalle de temps associé à la tranche actuelle en cours de production dans le jeu de données JSON avec des variables [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) et [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Actuellement Data Factory exige que le calendrier spécifié dans l’activité corresponde exactement à la planification spécifiée dans la **disponibilité** du jeu de données de sortie. Ainsi, **WindowStart**, **WindowEnd** et **SliceStart** et **SliceEnd** font toujours correspondre la même période de temps et une tranche de sortie unique.

Pour plus d’informations sur les différentes propriétés disponibles dans la section Disponibilité, consultez [Création de jeux de données](data-factory-create-datasets.md).

## Déplacement des données à partir de SQL Database vers le stockage blob

Mettons quelque chose en place en créant un pipeline qui copie les données d’une table de base de données SQL Azure vers un stockage sur objet blob Azure toutes les heures.

**Entrée : jeu de données de base données SQL Azure**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
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
	        "policy": {}
	    }
	}


La **fréquence**est définie sur **Heure** et **l’intervalle** sur **1**dans la section Disponibilité.

**Sortie : Jeu de données de stockage sur objet Blob Azure**

	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


La **fréquence**est définie sur **Heure** et **l’intervalle** sur **1**dans la section Disponibilité.



**Activité : activité de copie**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
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
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


L’exemple montre les sections Planification d’activité et Disponibilité d’un jeu de données défini à la fréquence toutes les heures. L’exemple montre comment vous pouvez utiliser **WindowStart** et **WindowEnd** pour sélectionner les données pertinentes pour l’exécution d’une activité et les copier sur un objet Blob avec le bon **folderPath**. **folderPath** est paramétré pour avoir un dossier distinct pour chaque heure.

Lorsque trois des tranches entre 8 et 11 h s’exécutent, et que les données dans la base de données SQL Azure sont les suivantes :

![Exemple d’entrée](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Après avoir déployé le pipeline, l’objet blob Azure est renseigné comme suit :

-	Fichier mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt avec données

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; sera remplacé par un vrai guid. Exemple de nom de fichier : Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
-	Fichier mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt avec données :

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
-	Fichier mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt sans données.


## Période active pour le pipeline

[Création de Pipelines](data-factory-create-pipelines.md) a présenté le concept de période active pour un pipeline spécifié par la définition des propriétés **start** et **end**.

Vous pouvez définir la date de début pour la période d’activité du pipeline dans le passé. Data Factory calcule (remplit postérieurement) automatiquement toutes les tranches de données dans le passé automatiquement et commence à les traiter.

## Traitement en parallèle des tranches de données
Vous pouvez configurer des tranches de données pour qu’elles soient exécutées en parallèle en définissant la propriété **concurrency** dans la section relative à la stratégie de l’activité JSON. Pour plus d’informations sur cette propriété, consultez [Création de pipelines](data-factory-create-pipelines.md).

## Réexécuter une tranche de données ayant échoué 
Vous pouvez surveiller l’exécution des tranches visuellement, avec tous les détails. Pour plus d’informations, consultez [Surveillance et gestion des pipelines à l’aide des](data-factory-monitor-manage-pipelines.md) panneaux du portail Azure (ou) de l’application [Surveillance et gestion](data-factory-monitor-manage-app.md).

Prenons l’exemple suivant, il montre les deux activités. Activity1 génère un jeu de données chronologique avec des tranches en sortie qui sont consommées en tant qu’entrée Activity2 pour générer le jeu de données de série de chronologie de la sortie finale.

![Tranche de données ayant échoué](./media/data-factory-scheduling-and-execution/failed-slice.png)

Le diagramme montre que, parmi les trois tranches récentes, il y a eu un échec, ce qui a généré une tranche 9 à 10 h pour Dataset2. Data Factory effectue automatiquement le suivi de la dépendance du jeu de données. Par conséquent, il ne lance pas l’activité sur la tranche 9 à 10 h en aval.

Les outils de surveillance et de gestion Data Factory vous permettent d’examiner en détail les journaux de diagnostic pour la tranche ayant échoué, et de trouver facilement la cause du problème pour le régler. Une fois le problème résolu, vous pouvez facilement lancer l’exécution de l’activité afin de générer la tranche ayant échoué. Pour plus d’informations sur la façon de lancer les réexécutions et comprendre les transitions d’état des tranches de données, consultez [Surveillance et gestion des pipelines à l’aide des panneaux du portail Azure (ou) de ](data-factory-monitor-manage-pipelines.md)[l’application Surveillance et gestion](data-factory-monitor-manage-app.md).

Une fois que vous avez relancé l’exécution de la tranche de 9-10 h pour **Dataset2**, Data Factory lance l’exécution de la tranche dépendante 9 à 10 h sur un jeu de données final.

![Réexécuter une tranche de données ayant échoué](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## Exécution d’activités dans une séquence
Vous pouvez chaîner deux activités (une après l’autre) en configurant le jeu de données de sortie d’une activité en tant que jeu de données d’entrée de l’autre activité. Les activités peuvent être dans le même pipeline ou dans des pipelines différents. La seconde activité s’exécute uniquement quand la première se termine correctement.

Considérez l’exemple suivant :

1.	Le pipeline P1 contient l’activité A1 nécessitant le jeu de données d’entrée externe D1 et produit le jeu de données de sortie D2.
2.	Le pipeline P2 contient l’activité A2 nécessitant le jeu de données d’entrée D2 et produit le jeu de données de sortie D3.

Dans ce scénario, les activités A1 et A2 sont dans des pipelines différents. L’activité A1 s’exécute lorsque les données externes seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. L’activité A2 s’exécute lorsque les tranches planifiées de D2 seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. S’il existe une erreur dans l’une des tranches du jeu de données D2, A2 n’est pas exécutée pour cette tranche jusqu’à ce que celle-ci devienne disponible.

La vue de diagramme se présente comme dans le diagramme suivant :

![Chaînage des activités dans deux pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Comme mentionné plus tôt, les activités peuvent être dans le même pipeline. La vue de diagramme avec les deux activités dans le même pipeline se présente comme dans le diagramme suivant :

![Chaînage des activités dans le même pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### Copier de manière séquentielle
Il est possible d’exécuter plusieurs opérations de copie l’une après l’autre, de manière séquentielle/ordonnée. Si, par exemple, vous avez deux activités de copie dans un pipeline : (ActivitédeCopie1 et ActivitédeCopie2) avec les jeux de données de sortie de données d’entrée suivants :

Activitédecopie1

Jeu de données d'entrée. Sortie : Dataset2.

Activitédecopie2

Entrée : Jeudedonnées2. Sortie : Jeudedonnées3.

ActivitédeCopie2 s’exécute uniquement si ActivitédeCopie1 s’est exécutée avec succès et que JeudeDonnées2 est disponible.

Voici l’exemple de pipeline JSON :

	{
		"name": "ChainActivities",
	    "properties": {
			"description": "Run activities in sequence",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "copyBehavior": "PreserveHierarchy",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset1"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob1ToBlob2",
	                "description": "Copy data from a blob to another"
	            },
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset3"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob2ToBlob3",
	                "description": "Copy data from a blob to another"
	            }
	        ],
	        "start": "2016-08-25T01:00:00Z",
	        "end": "2016-08-25T01:00:00Z",
	        "isPaused": false
	    }
	}

Notez que dans l’exemple, le jeu de données de sortie de la première activité de copie (Dataset2) est spécifié en tant qu’entrée pour la deuxième activité. Par conséquent, la deuxième activité s’exécute uniquement lorsque le jeu de données de sortie de la première activité est prêt.

Dans l’exemple, ActivitédeCopie2 peut avoir une entrée différente, par exemple JeudeDonnées3, mais vous spécifiez JeudeDonnées2 en tant qu’entrée pour ActivitédeCopie2, afin que l’activité ne s’exécute pas avant la fin d’ActivitédeCopie1. Par exemple :

Activitédecopie1

Entrée : Jeudedonnées1. Sortie : Dataset2.

Activitédecopie2

Entrées : Jeudedonnées3, Jeudedonnées2. Sortie : Jeudedonnées4.

	{
		"name": "ChainActivities",
	    "properties": {
			"description": "Run activities in sequence",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "copyBehavior": "PreserveHierarchy",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset1"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlobToBlob",
	                "description": "Copy data from a blob to another"
	            },
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset3"
	                    },
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset4"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob3ToBlob4",
	                "description": "Copy data from a blob to another"
	            }
	        ],
	        "start": "2017-04-25T01:00:00Z",
	        "end": "2017-04-25T01:00:00Z",
	        "isPaused": false
	    }
	}


Notez que dans l’exemple, deux jeux de données d’entrée sont spécifiés pour la deuxième activité de copie. Quand plusieurs entrées sont spécifiées, seul le premier jeu de données d’entrée est utilisé pour copier des données, mais les autres jeux de données sont utilisés en tant que dépendances. L’exécution d’ActivitédeCopie2 démarre uniquement quand les conditions suivantes sont remplies :

- ActivitédeCopie1 s’est terminée avec succès et JeudeDonnées2 est disponible. Ce jeu de données n’est pas utilisé lors de la copie des données vers JeudeDonnées4. Il sert uniquement de dépendance de planification pour ActivitédeCopie2.
- JeudeDonnées3 est disponible. Ce jeu de données représente les données qui sont copiées vers la destination.



## Modélisation des jeux de données avec des fréquences différentes

Dans les exemples, les fréquences de planification des jeux de données d’entrée et de sortie et l’intervalle d’activité sont les mêmes. Certains scénarios exigent que la fréquence de génération d’une sortie à soit différente de celles d’une ou de plusieurs entrées. Data factory prend en charge la modélisation de ces scénarios.

### Exemple 1 : production d’un rapport de sortie quotidien pour les données d’entrée est disponible toutes les heures

Imaginez un scénario dans lequel nous avons entré des données de mesure à partir de capteurs disponibles toutes les heures dans un stockage Azure Blob. Vous voulez générer un rapport d’agrégation quotidien avec des statistiques, comme les valeurs moyenne, maximum et minimum pour la journée avec une [Activité Hive Data Factory](data-factory-hive-activity.md).

Voici ce que vous pouvez modéliser ce scénario avec data factory :

**Jeu de données d'entrée**

Les fichiers d’entrée des heures sont supprimés dans le dossier pour le jour donné. La disponibilité de l’entrée est définie sur **Toutes les heures** (fréquence : Heure, intervalle: 1).

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Jeu de données de sortie**

Un fichier de sortie est créé chaque jour dans le dossier pour la journée. Disponibilité de sortie a pour valeur **Quotidien** (fréquence : jour et intervalle : 1).


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Activité : activité Hive dans un pipeline**

Le script Hive reçoit les informations de *DateTime* en tant que paramètres qui utilisent la variable **WindowStart** comme indiqué dans l’extrait de code suivant. Le script Hive utilise cette variable pour charger les données à partir du dossier correspondant à la journée et exécuter l’agrégation pour générer la sortie.

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
		            "inputs": [
		                {
		                    "name": "AzureBlobInput"
		                }
		            ],
		            "outputs": [
		                {
		                    "name": "AzureBlobOutput"
		                }
		            ],
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

Le diagramme suivant illustre le scénario du point de vue de la dépendance des données.

![Dépendance des données](./media/data-factory-scheduling-and-execution/data-dependency.png)

La tranche de sortie dépend des 24 tranches horaires depuis l’ensemble de données en entrée. Data Factory calcule automatiquement ces dépendances en déterminant les tranches de données d’entrée qui tombent dans la même période que la tranche de données à générer. Si une des 24 tranches d’entrée n’est pas disponible, Data Factory attend que la tranche d’entrée soit prête avant de lancer l’exécution d’activité quotidienne.


### Exemple 2 : spécifier les dépendances avec des expressions et des fonctions Data Factory

Prenons en compte un autre scénario. Supposez que vous avez une activité Hive qui traite deux jeux de données d’entrée. Un d’eux a de nouvelles données tous les jours, mais l’autre obtient de nouvelles données toutes les semaines. Supposons que vous vouliez faire la jonction entre les deux entrées et générer une sortie quotidiennement.

L’approche simple consistant pour Data Factory à déterminer des tranches d’entrée appropriées à traiter en alignant la période de temps de la tranche de données en sortie ne fonctionne plus.

Vous devez spécifier (pour chaque exécution d’activité que Data Factory doit utiliser) les tranches de données de la semaine précédente pour les jeux de données d’entrée. Vous utilisez les fonctions Azure Data Factory comme indiqué dans l’extrait de code suivant pour implémenter ce comportement.

**Entrée1 : Azure Blob**

La première entrée est l’objet Azure Blob mis à jour quotidiennement.

	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Entrée2 : objet Blob Azure**

Entrée2 est l’objet Azure Blob mis à jour chaque semaine.

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**Sortie : objet Blob Azure**

Un fichier de sortie est créé chaque jour dans le dossier pour la journée. La disponibilité de sortie est définie sur **Quotidiennement** (fréquence : jour et intervalle : 1).

	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Activité : activité Hive dans un pipeline**

L’activité Hive accepte les deux entrées et génère une tranche de sortie tous les jours. Vous pouvez spécifier la tranche de sortie de tous les jours dépendant de la tranche de semaine précédente pour la sortie hebdomadaire comme suit.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   }
	     ]
	   }
	}


## Variables système et fonctions Data Factory   

Pour obtenir la liste des fonctions et variables système prises en charge par Azure Data Factory, consultez [Variables système et fonctions Data Factory](data-factory-functions-variables.md).

## Examen approfondi de la dépendance de données

Pour générer une tranche de jeu de données en exécutant une activité, Data Factory utilise le *modèle de dépendance* pour déterminer les relations entre les jeux de données consommés et générés par une activité.

L’intervalle de temps des jeux de données d’entrée requis pour générer la tranche de jeu de données de sortie s’appelle la *période de dépendance*.

Une exécution d’activité génère une tranche de jeu de données seulement après que les tranches de données dans les jeux de données d’entrée au sein de la période de dépendance sont disponibles. En d’autres termes, toutes les tranches d’entrée constituant la période de dépendance doivent être à l’état **Prêt** pour que la tranche de jeu de données de sortie puisse être générée par l’exécution de l’activité.

Pour générer la tranche de jeu de données [**début**, **fin**], une fonction mettant en adéquation la tranche de jeu de données avec la période de dépendance doit exister. Cette fonction est essentiellement une formule qui convertit le début et la fin de la tranche de jeu de données au début et à la fin de la période de dépendance. Plus formellement :

	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

**F** et **g** sont des fonctions de mappage qui calculent le début et la fin de la période de dépendance pour chaque activité d’entrée.

Comme on le voit dans les exemples, la période de dépendance est identique à la période de la tranche de données qui est produite. Dans ces cas, Data Factory calcule automatiquement les tranches d’entrée qui se situent dans la période de dépendance.

Par exemple, dans l’exemple d’agrégation où la sortie est générée quotidiennement et des données d’entrée sont disponibles toutes les heures, la période de tranche de données est de 24 heures. Data Factory recherche les tranches d’entrée chaque heure pour chaque intervalle et rend la tranche de sortie dépendante d’une tranche d’entrée.

Vous pouvez également fournir votre propre correspondance pour la période de dépendance comme indiqué dans l’exemple, où une des entrées était hebdomadaire et la tranche de sortie est générée au quotidien.

## Dépendance et validation de données

Un jeu de données peut avoir une stratégie de validation définie qui spécifie comment les données générées par l’exécution d’une tranche peuvent être validées avant qu’il soit prêt à la consommation. Consultez [Création de jeux de données](data-factory-create-datasets.md) pour plus d’informations.

Dans ce cas, une fois que la tranche a terminé l’exécution, l’état de la tranche de sortie devient **Attente** avec un sous-état **Validation**. Une fois les tranches validées, l’état de la tranche passe à **prêt**.

Si une tranche de données a été générée mais n’a pas réussi la validation, l’activité s’exécute pour les tranches en aval dépendant de cette tranche qui ne sont pas traitées.

Les différents états des tranches de données dans Data Factory sont couverts dans l’article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

## Données externes

Un jeu de données peut être marqué en tant qu’externe (comme indiqué dans l’extrait de code JSON suivant), l’implication n’a pas été générée avec Data Factory. Dans ce cas, la stratégie de jeu de données peut avoir un ensemble de paramètres décrivant la stratégie de validation et de réexécution du jeu de données. Consultez [Création de pipelines](data-factory-create-pipelines.md) pour obtenir la description de toutes les propriétés.

Similaires aux jeux de données produits par Data Factory, les tranches de données pour les données externes doivent être prêtes avant que les tranches dépendantes puissent être traitées.

	{
		"name": "AzureSqlInput",
		"properties":
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties":
			{
				"tableName": "MyTable"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy":
			{
				"externalData":
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		}
	}


## Pipeline onetime
Vous pouvez créer et planifier un pipeline pour qu’il s’exécute périodiquement (par exemple  toutes les heures ou tous les jours) en fonction de l’heure de début et de l’heure de fin que vous spécifiez dans la définition du pipeline. Pour plus d’informations, consultez [Planification des activités](#scheduling-and-execution). Vous pouvez également créer un pipeline qui ne s’exécute qu’une seule fois. Pour ce faire, vous définissez la propriété **pipelineMode** dans la définition du pipeline sur la valeur **onetime**, comme indiqué dans l’exemple JSON suivant. La valeur par défaut de cette propriété est **scheduled** (planifié).

	{
	    "name": "CopyPipeline",
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
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

Notez les points suivants :

- Les heures de **début** et de **fin** ne sont pas spécifiées.
- La **disponibilité** des jeux de données d’entrée et de sortie est spécifiée (**fréquence** et **intervalle**) même si les valeurs ne sont pas utilisées par Data Factory.
- La vue schématique n’affiche pas les pipelines à usage unique (onetime). Ce comportement est normal.
- Les pipelines à usage unique ne peuvent pas être mis à jour. Vous pouvez cloner un pipeline à usage unique, le renommer, mettre à jour ses propriétés et le déployer pour en créer un autre.

<!---HONumber=AcomDC_0928_2016-->