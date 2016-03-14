<properties 
	pageTitle="Planification et exécution avec Data Factory" 
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
	ms.date="01/27/2016" 
	ms.author="spelluru"/>

# Planification et exécution avec Data Factory
  
Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. Cet article s’appuie sur les articles [Création de pipelines](data-factory-create-pipelines.md) et [Création de groupes de données](data-factory-create-datasets.md) et suppose que vous avez des notions de base sur les concepts de modèle de données Data Factory : activité, pipelines, services connexes et des groupes de données.

## Planification des activités

Grâce à la section **planificateur** de l’activité JSON, vous pouvez planifier l’activité pour qu’elle s’exécute de façon récurrente. Par exemple, vous pouvez planifier une activité toutes les heures comme suit :

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![Exemple de planificateur](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Comme indiqué précédemment, la spécification d’un calendrier pour l'activité crée une série de fenêtres récurrentes. Les fenêtres récurrentes sont une série d’intervalles de temps fixes contigus, qui ne se chevauchent pas. Ces fenêtres récurrentes logiques pour l'activité sont appelés **fenêtres d'activité**.
 
Pour la fenêtre d’activité en cours d’exécution, l’intervalle de temps associé à la fenêtre d’activité est accessible par le biais des variables système **WindowStart** et **WindowEnd** de l’activité de JSON. Vous pouvez utiliser ces variables à d’autres fins dans votre activité JSON et les scripts associés à l’activité, notamment pour la sélection des données dans les jeux de données d’entrée et de sortie représentant les données de séries chronologiques.

La propriété **scheduler** prend en charge les mêmes sous-propriétés que la propriété **availability** dans un jeu de données. Pour en savoir plus sur les différentes propriétés disponibles pour le planificateur, notamment la programmation selon un décalage chronologique spécifique (dans le but de définir le mode de manière à faire coïncider le traitement avec le début ou la fin de l’intervalle de la fenêtre d’activité), voir [Disponibilité du jeu de données](data-factory-create-datasets.md#Availability).

## Jeux de données et tranches de données de série chronologique

Les données de série chronologique sont une séquence continue de points de données comprenant généralement des mesures successives effectuées pendant un certain intervalle de temps. Parmi les données de série chronologique on trouve : des données de capteur, des données pour application de télémétrie.

Avec Azure Data Factory, vous pouvez traiter les données de série par lot pendant l’exécution de l’activité. En règle générale, il existe des cadences périodiques régissant le rythme auquel les données d’entrée arrivent et les données de sortie sont générées. Cette cadence est modélisée dans la section **Disponibilité** du jeu de données comme suit :

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Chaque unité de données consommée et produite pendant l’exécution d’une activité est appelée **tranche** de données. Le diagramme ci-dessous montre un exemple d’activité contient un jeu de données de série chronologique, dont la propriété Disponibilité est définie sur une fréquence de «Toutes les heures ».

![Planificateur de disponibilité](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Les tranches de données recueillies toutes les heures pour le jeu de données d’entrée et de sortie sont affichées dans le diagramme ci-dessus. Le diagramme montre 3 tranches d’entrée prêtes pour le traitement et l’exécution de l’activité entre 10 et 11 h en cours, et générant la tranche de sortie de 10 à 11 h.

L’intervalle de temps associé à la tranche actuelle en cours de production est accessible dans le jeu de données JSON avec des variables **SliceStart** et **SliceEnd**.

Pour plus d’informations sur les différentes propriétés disponibles dans la section Disponibilité, reportez-vous à l’article [Création de jeux de données](data-factory-create-datasets.md).

## Exemple : activité de copie déplaçant des données de SQL Azure vers un objet blob Azure

Réunissons les informations et réexaminons le modèle d’activité de copie affiché dans l’article [Création de Pipelines](data-factory-create-pipelines.md) qui copie les données d’une table SQL Azure vers un objet blob Azure toutes les heures.

**Entrée : jeu de données SQL Azure**

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


Notez que la **fréquence**est définie sur **Heure** et l’**intervalle** sur **1**dans la section **Disponibilité**.

**Sortie : jeu de données Blob Azure**
	
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


Notez que la **fréquence**est définie sur **Heure** et l’**intervalle** sur **1**dans la section **Disponibilité**.



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


L’exemple ci-dessus montre les sections Planification d’activité et Disponibilité d’un jeu de données défini à la fréquence toutes les heures. L’exemple montre comment vous pouvez utiliser les variables **WindowStart** et **WindowEnd** pour sélectionner les données pertinentes pour l’exécution de l’activité et l’envoyer vers un objet blob avec le chemin dynamique **folderPath** paramétré pour avoir le dossier toutes les heures.

Lorsque 3 des tranches entre 8 et 11 h s’exécutent, c’est à cela que ressemble un exemple de table et d’objet blob Azure.

Supposez que les données d’Azure SQL soient les suivantes :

![Exemple d’entrée](./media/data-factory-scheduling-and-execution/sample-input-data.png)

En déployant le pipeline ci-dessus, l’objet blob Azure sera renseigné comme suit :

1.	Fichier mypath/2015/1/1/8/Data.<Guid>.txt avec données 

		10002345,334,2,2015-01-01 08:24:00.3130000
		10002345,347,15,2015-01-01 08:24:00.6570000
		10991568,2,7,2015-01-01 08:56:34.5300000

	**Remarque :** <Guid>sera remplacé par un guid actuel. Exemple de nom de fichier : Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	Fichier mypath/2015/1/1/9/Data.<Guid>.txt avec données :

		10002345,334,1,2015-01-01 09:13:00.3900000
		24379245,569,23,2015-01-01 09:25:00.3130000
		16777799,21,115,2015-01-01 09:47:34.3130000
3.	Fichier mypath/2015/1/1/10/Data.<Guid>.txt sans données.


## Tranches de données, période active pour l’exécution simultanée du pipeline et de la tranche.

L’article [Création de Pipelines](data-factory-create-pipelines.md) a présenté le concept de période active pour un pipeline spécifié par la définition des propriétés **start** et **end** du pipeline.
 
Vous pouvez définir la date de début de la période active de pipeline dans le passé et Data Factory calcule automatiquement (remplissage de l’arrière-plan) toutes les tranches de données du passé et commence à les traiter.

Les tranches de données renseignées en arrière-plan permettent leur configuration en parallèle. Vous pouvez le faire en définissant la propriété Simultané dans la section **stratégie** de l’activité de JSON comme indiqué dans l’article [Création de pipelines](data-factory-create-pipelines.md).

## Réexécution des tranches de données ayant échoué et suivi de la dépendance de données automatique

Vous pouvez surveiller l’exécution des tranches visuellement, avec tous les détails. Consultez l’article [Surveillance et gestion des pipelines](data-factory-monitor-manage-pipelines.md) pour plus de détail.

Prenons l’exemple suivant, il montre les deux activités. Activity1 génère un jeu de données chronologique avec des tranches en sortie qui sont consommées en tant qu’entrée Activity2 pour générer le jeu de données de série de chronologie de la sortie finale.

![Tranche de données ayant échoué](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

Le diagramme ci-dessus montre que, parmi les 3 tranches récentes, il y a eu un échec, ce qui généré une tranche 9 à 10 h pour **Dataset2**. Data Factory effectue automatiquement le suivi de la dépendance du jeu de données et, par conséquent, retient l’exécution de l’activité sur la tranche 9 à 10 h en aval.


Les outils de surveillance et de gestion Data Factory vous permettent d’examiner en détail les journaux de diagnostic pour la tranche ayant échoué, et de trouver facilement la cause du problème pour le régler. Une fois le problème résolu, vous pouvez facilement lancer l’exécution de l’activité afin de générer la tranche ayant échouée. Pour plus d’informations sur la façon de lancer les réexécutions, comprendre les états de transition des tranches de données, consultez l’article [Analyse et gestion](data-factory-monitor-manage-pipelines.md).

Une fois que vous avez relancé l’exécution et que la tranche de 9-10 h pour dataset2 est prête, Data Factory lance l’exécution de la tranche dépendante 9 à 10 h sur un jeu de données final comme indiqué dans le schéma ci-dessous.

![Réexécuter une tranche de données ayant échoué](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

Pour de plus amples informations sur la spécification et le suivi de dépendances de chaîne complexe des activités et des groupes de données de suivi, reportez-vous aux sections ci-dessous.

## Chaînage des activités
Vous pouvez chaîner deux activités en utilisant le jeu de données de sortie d’une activité en tant que jeu de données d’entrée de l’autre activité. Les activités peuvent être dans le même pipeline ou dans des pipelines différents. La seconde activité s’exécute uniquement quand la première se termine correctement.

Considérez l’exemple suivant :
 
1.	Le pipeline P1 contient l’activité A1 nécessitant le jeu de données d’entrée externe D1 et produit le jeu de données de **sortie** **D2**.
2.	Le pipeline P2 contient l’activité A2 nécessitant le jeu de données d’**entrée** **D2** et produit le jeu de données de sortie D3.
 
Dans ce scénario, l’activité A1 s’exécutera lorsque les données externes seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. L’activité A2 s’exécutera lorsque les tranches planifiées de D2 seront disponibles et que la fréquence de disponibilité planifiée sera atteinte. S’il existe une erreur dans l’une des tranches du jeu de données D2, A2 ne sera pas exécutée pour cette tranche jusqu’à ce que celle-ci devienne disponible.

La vue de diagramme se présente comme suit :

![Chaînage des activités dans deux pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

La vue de diagramme avec les deux activités dans le même pipeline se présente comme suit :

![Chaînage des activités dans le même pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)


## Modélisation des jeux de données avec des fréquences différentes

Dans les exemples ci-dessus, les fréquences de planification des jeux de données d’entrée et de sortie et l’intervalle d’activité sont les mêmes. Certains scénarios exigent que la fréquence de génération d’une sortie à soit différente de celles d’une ou de plusieurs entrées. Data factory prend en charge la modélisation de ces scénarios.

### Exemple 1 : la production d’un rapport de sortie quotidien pour les données d’entrée est disponible toutes les heures

Imaginez un scénario dans lequel nous entrons des données de mesure issues de capteurs disponibles toutes les heures dans les objets Blob Azure et souhaitons générer un rapport agrégeant quotidiennement les statistiques telles que la moyenne, le maximum, le minimum, etc.... pour la journée avec l’[activité Hive](data-factory-hive-activity.md) de Data Factory.

Voici ce que vous pouvez modéliser avec data factory :

**Entrée jeu de données d’objet blob Azure :**

Les fichiers d’entrée des heures sont supprimés dans le dossier pour le jour donné. La disponibilité de l’entrée est définie toutes les heures (fréquence : Heure, intervalle: 1).

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

**Sortie : Jeu de données d’objet blob Azure**

Un fichier de sortie est placé chaque jour dans le dossier pour la journée. Disponibilité de sortie a pour valeur Quotidien (fréquence : jour et intervalle : 1).


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

Le script Hive reçoit les informations de date en tant que paramètres et utilise la variable **WindowStart** comme indiqué ci-dessous. Le script Hive utilise cette variable pour charger les données à partir du dossier correspondant à la journée et exécuter l’agrégation pour générer la sortie.

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
		                    "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Voilà comment le tout se présente du point de vue de la dépendance des données.

![Dépendance de données](./media/data-factory-scheduling-and-execution/data-dependency.png)

La tranche de sortie dépend des 24 tranches horaires depuis l’ensemble de données en entrée. Data Factory calcule automatiquement ces dépendances en déterminant les tranches de données d’entrée qui tombent dans la même période que la tranche de données à générer. Si l’une des 24 tranches d’entrée n’est pas disponible (en raison du traitement générant la tranche en amont par exemple), Data Factory attend que la tranche d’entrée soit prête avant de lancer l’exécution de l’activité quotidienne.


### Exemple 2 : spécifier les dépendances avec des expressions et des fonctions Data Factory

Prenons en compte un autre scénario. Supposons que vous disposez d’une activité Hive qui traite deux jeux de données d’entrée. L’un d’eux reçoit des nouvelles données tous les jours, mais l’autre obtient de nouvelles données toutes les semaines. Supposons que vous vouliez faire la jonction entre les deux entrées et générer une sortie quotidiennement.
 
L’approche simple consistant pour Data Factory à déterminer des tranches d’entrée appropriées à traiter en incluant des tranches de données en entrée dans la période de temps de la tranche de données en sortie ne fonctionne plus.

Vous devez trouver un moyen de spécifier (pour chaque exécution d’activité que Data Factory doit utiliser) les tranches de données de la semaine précédente pour les jeux de données d’entrée. Vous pouvez le faire à l’aide de fonctions Data Factory comme indiqué ci-dessous.

**Entrée1 : Azure Blob**

La première entrée est mise à jour avec l’objet blob Azure **quotidiennement**.
	
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

Entrée2 est mis à jour avec l’objet blob Azure de manière **hebdomadaire**.

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

Un fichier de sortie est placé chaque jour dans le dossier pour la journée. La disponibilité de sortie est définie sur Quotidiennement (fréquence : jour et intervalle : 1).
	
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

L’activité Hive accepte les 2 entrées et génère une tranche de sortie tous les jours. Vous pouvez spécifier la tranche de sortie de tous les jours dépendant de la tranche de semaine pour la sortie hebdomadaire comme suit.
	
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
	            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Pour obtenir la liste des fonctions et variables système prises en charge par Azure Data Factory, consultez [Variables système et fonctions Data Factory](data-factory-functions-variables.md)

## Examen approfondi de la dépendance de données

Pour générer une tranche de jeu de données en exécutant une activité, Data Factory utilise le **modèle de dépendance** pour déterminer les relations entre le jeu de données consommées par une activité et le jeu de données généré par une activité.

L’intervalle de temps des jeux de données d’entrée requis pour générer la tranche de jeu de données de sortie s’appelle la **période de dépendance**.

Une exécution d’activité génère une tranche de jeu de données seulement après que les tranches de données dans les jeux de données d’entrée au sein de la période de dépendance sont disponibles. Cela signifie que toutes les tranches d’entrée constituant la période de dépendance doivent être à l’état **prêt** pour que la tranche de jeu de données de sortie puisse être générée par l’exécution de l’activité.

Pour générer la tranche de jeu de données [début, fin], une fonction mettant en adéquation la tranche de jeu de données avec la période de dépendance doit exister. Cette fonction est essentiellement une formule qui convertit le début et la fin de la tranche de jeu de données au début et à la fin de la période de dépendance. Plus formellement,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

où f et g sont des fonctions de mappage qui calculent le début et la fin de la période de dépendance pour chaque activité d’entrée.

Comme on le voit dans les exemples ci-dessus, dans la plupart des cas, la période de dépendance est identique à la période de la tranche de données à produire. Dans ces cas, Data Factory calcule automatiquement les tranches d’entrée qui se situent dans la période de dépendance.

Par exemple : dans l’exemple d’agrégation ci-dessus où la sortie est générée quotidiennement et des données d’entrée sont disponibles toutes les heures, la période de tranche de données est de 24 heures. Data Factory recherche les tranches d’entrée chaque heure pour chaque intervalle et rend la tranche de sortie dépendante d’une tranche d’entrée.

Vous pouvez également fournir votre propre correspondance pour la période de dépendance comme indiqué dans l’exemple ci-dessus, où une des entrées était hebdomadaire et la tranche de sortie est générée au quotidien.
   
## Dépendance et validation de données

Un jeu de données peut éventuellement avoir une stratégie de validation définie qui spécifie comment les données générées par l’exécution d’une tranche peuvent être validées avant qu’il soit prêt à la consommation. Consultez l’article [Création de jeux de données](data-factory-create-datasets.md) pour plus d’informations.

Dans ce cas, une fois que la tranche a terminé l’exécution, l’état de la tranche de sortie devient **Attente** avec un sous-état **Validation**. Une fois les tranches validées, l’état de la tranche passe à **prêt**.
   
Si une tranche de données a été générée mais n’a pas réussi la validation, l’activité s’exécute pour les tranches en aval en fonction de la tranche dont la validation a échoué et n’est pas traitée.

Les différents états des tranches de données dans Data Factory sont couverts dans l’article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md).

## Données externe

Un jeu de données peut être marqué en tant qu’externe (comme indiqué dans JSON ci-dessous), l’implication na pas été généré avec Azure Data Factory. Dans ce cas, la stratégie de jeu de données peut avoir un ensemble de paramètres décrivant la stratégie de validation et de réexécution du jeu de données. Consultez [Création de pipelines](data-factory-create-pipelines.md) pour obtenir la description de toutes les propriétés.

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






  









 
 












      

  

<!---HONumber=AcomDC_0302_2016-->