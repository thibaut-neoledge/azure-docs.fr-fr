<properties 
	pageTitle="Transformation des données : traiter et transformer des données | Microsoft Azure" 
	description="Apprenez-en plus sur la transformation de données dans Azure Data Factory. Transformez et traitez des données dans un cluster Azure HDInsight ou un batch Azure." 
	keywords="transformation des données, traiter les données, transformer les données, activité de transformation"
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
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# En savoir plus sur la transformation et l’analyse des données dans Azure Data Factory
Cet article explique les activités de transformation des données dans Azure Data Factory, afin que vous sachiez comment ADF transforme et traite vos données brutes pour en tirer des prévisions et des informations. L'activité de transformation s'exécute dans un environnement de calcul comme un cluster Azure HDInsight ou un Azure Batch. Ce document contient également des liens vers des articles montrant comment utiliser des activités de transformation spécifiques.
 
Azure Data Factory prend en charge les activités de transformation suivantes, qui peuvent être ajoutées à des [pipelines](data-factory-create-pipelines.md), soit individuellement soit de façon chaînée avec une autre activité.

Activités de transformation des données | Environnement de calcul 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Activités Machine Learning : exécution par lot et mise à jour de la ressource](data-factory-azure-ml-batch-execution-activity.md) | Microsoft Azure 
[Procédure stockée](data-factory-stored-proc-activity.md) | SQL Azure, Azure SQL Data Warehouse ou SQL Server |
[Langage U-SQL du service Analytique Data Lake](data-factory-usql-activity.md) | Service Analytique Azure Data Lake 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou Azure Batch
   
> [AZURE.NOTE] 
Vous pouvez utiliser l'activité MapReduce pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez la page [Appeler des programmes Spark à partir d’Azure Data Factory](data-factory-spark.md) pour plus d’informations. Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight si R est installé. Consultez la page [Exécuter des scripts R avec Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).
 

Vous devez créer un service lié à l'environnement de calcul puis l’utiliser lorsque vous définissez une activité de transformation. Il existe deux types d'environnements de calcul pris en charge par Data Factory.

1. **À la demande** : dans ce cas, l'environnement de calcul est entièrement géré par Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Les utilisateurs peuvent configurer et contrôler les paramètres granulaires de l’environnement de calcul à la demande pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage.
2. **Apport de votre propre environnement** : dans ce cas, vous pouvez inscrire votre propre environnement de calcul (par exemple un cluster HDInsight) en tant que service lié dans Data Factory. C’est vous qui gérez l'environnement de calcul et le service Data Factory l'utilise pour exécuter les activités.

Consultez l’article [Services liés de calcul](data-factory-compute-linked-services.md) pour en savoir plus sur les services de calcul liés pris en charge par Data Factory.

<!---HONumber=AcomDC_0629_2016-->