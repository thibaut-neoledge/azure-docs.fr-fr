<properties 
	pageTitle="Activités de transformation de données | Microsoft Azure" 
	description="Découvrez comment utiliser le service Azure Data Factory pour transformer et analyser des données." 
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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Transformation et analyse en utilisant Azure Data Factory

## Vue d’ensemble
Les activités de transformation dans Azure Data Factory transforment et traitent vos données brutes en prévisions et en analyses. L'activité de transformation s'exécute dans un environnement de calcul comme un cluster Azure HDInsight ou un Azure Batch. Azure Data Factory prend en charge les activités de transformation suivantes, qui peuvent être ajoutées à des [pipelines](data-factory-create-pipelines.md), soit individuellement soit de façon chaînée avec une autre activité.


Activité de transformation | Environnement de calcul 
----------------------- | --------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] [Diffusion Hadoop](https://msdn.microsoft.com/library/mt185698.aspx) | HDInsight [Hadoop] [Machine Learning Batch Execution](data-factory-azure-ml-batch-execution-activity.md) | Microsoft Azure 
[Procédure stockée](data-factory-stored-proc-activity.md) | Azure SQL | 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou Azure Batch    

Vous devez créer un service lié à l'environnement de calcul puis l’utiliser lorsque vous définissez une activité de transformation. Il existe deux types d'environnements de calcul pris en charge par Data Factory.

1. **À la demande** : dans ce cas, l'environnement de calcul est entièrement géré par Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Les utilisateurs peuvent configurer et contrôler les paramètres granulaires de l’environnement de calcul à la demande pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage. 
2. **Apport de votre propre environnement** : dans ce cas, vous pouvez enregistrer votre propre environnement de calcul (par exemple un cluster HDInsight) en tant que service lié dans Data Factory. C’est vous qui gérez l'environnement de calcul et le service Data Factory l'utilise pour exécuter les activités. 

Consultez l'article [Services liés de calcul](data-factory-compute-linked-services.md) pour en savoir plus sur les services de calcul liés pris en charge par Data Factory.

## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-transformation-activities.md).

<!---HONumber=Oct15_HO3-->