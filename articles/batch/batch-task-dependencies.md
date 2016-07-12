<properties
	pageTitle="Dépendances de tâches dans Azure Batch | Microsoft Azure"
	description="Créez des tâches qui dépendent de l’exécution d’autres tâches pour le traitement de charges de travail de type MapReduce ou Big Data dans Azure Batch."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/29/2016"
	ms.author="marsma" />

# Dépendances de tâches dans Azure Batch

Si vous souhaitez traiter une charge de calcul de type MapReduce dans le cloud, si vous disposez d’une tâche de traitement de données dont les tâches peuvent être exprimées sous la forme d’un graphique acyclique dirigé (DAG), ou si vous exécutez tout autre travail dont les tâches en aval dépendent de la sortie des tâches en amont, la fonctionnalité de dépendances de tâche d’Azure Batch peut être une solution.

Cette fonctionnalité vous permet de créer des tâches planifiées pour s’exécuter sur des nœuds de calcul à condition qu’une ou plusieurs autres tâches se soient correctement exécutées. Par exemple, vous pouvez créer un travail qui restitue chaque image d’un film 3D avec des tâches parallèles distinctes et dont la tâche finale (la « tâche de fusion ») fusionne les images restituées dans la vidéo complète uniquement après la restitution de l’ensemble des images.

Vous pouvez créer des tâches qui dépendent d’autres tâches dans une relation un-à-un ou un-à-plusieurs, ou même une dépendance de plages dans laquelle une tâche dépend de la bonne exécution d’un groupe de tâches au sein d’une plage d’ID de tâche spécifique. Vous pouvez combiner ces trois scénarios de base pour créer des relations plusieurs-à-plusieurs.

## Dépendances de tâches avec Batch.NET

Cet article explique comment configurer les dépendances de tâches à l’aide de la bibliothèque [Batch .NET][net_msdn]. Nous allons tout d’abord vous montrer comment [activer la dépendance de tâches](#enable-task-dependency) dans vos projets, puis vous expliquer brièvement comment [configurer une tâche avec des dépendances](#create-dependent-tasks). Pour fini, nous passerons en revue les [scénarios de dépendance](#dependency-scenarios) pris en charge par Batch.

## Activation des dépendances de tâches

Pour utiliser les dépendances de tâches dans votre application Batch, vous devez d’abord indiquer au service Batch que la tâche utilisera des dépendances de tâches. Dans Batch.NET, activez la dépendance de tâches sur votre [CloudJob][net_cloudjob] en définissant sa propriété [UsesTaskDependencies][net_usestaskdependencies] sur `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Dans l’extrait de code ci-dessus, « batchClient » est une instance de la classe [BatchClient][net_batchclient].

## Création de tâches dépendantes

Pour créer une tâche qui dépend de l’exécution d’une ou plusieurs autres tâches, vous devez indiquer à Batch que la tâche « dépend » des autres tâches. Dans Batch.NET, configurez la propriété [CloudTask][net_cloudtask].[DependsOn][net_dependson] avec une instance de la classe [TaskDependencies][net_taskdependencies] \:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Cet extrait de code crée une tâche avec l’ID « Flowers » qui est programmée pour s’exécuter sur un nœud de calcul uniquement après la réussite de l’exécution des tâches associées aux ID « Rain » et « Sun ».

 > [AZURE.NOTE] Une tâche est considérée comme réussie lorsqu’elle se trouve à l’état **terminé** et que son **code de sortie** est `0`. Dans Batch.NET, la valeur de propriété [CloudTask][net_cloudtask].[State][net_taskstate] doit être `Completed` et la valeur de propriété [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] de CloudTask doit être de `0`.

## Scénarios de dépendance

Vous pouvez utiliser trois scénarios de dépendance de tâches de base dans Azure Batch : un-à-un, un-à-plusieurs et dépendance de plage d’ID de tâche. Ces scénarios peuvent être combinés pour créer un quatrième scénario : plusieurs-à-plusieurs.

 Scénario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemple | |
 :-------------------: | ------------------- | -------------------
 [Un-à-un](#one-to-one) | *taskB* dépend de *taskA* <p/> *taskB* n’est pas planifié pour s’exécuter tant que l’exécution de *taskA* n’est pas terminée | ![Schéma : dépendance de tâches un-à-un][1]
 [Un-à-plusieurs](#one-to-many) | *taskC* dépend de *taskA* et de *taskB* <p/> *taskC* n’est pas planifié pour s’exécuter tant que l’exécution de *taskA* et *taskB* n’est pas terminée | ![Schéma : dépendance de tâches un-à-plusieurs][2]
 [Plage d’ID de tâche](#task-id-range) | *taskD* dépend d’une plage de tâches <p/> *taskD* n’est pas planifié pour s’exécuter tant que l’exécution des tâches associées aux ID *1* à *10* n’est pas terminée | ![Schéma : dépendance de plage d’ID de tâche][3]

>[AZURE.TIP] Vous pouvez créer des relations **plusieurs-à-plusieurs** où, par exemple, les tâches C, D, E et F dépendent toutes des tâches A et B. Cela est utile, par exemple, dans les scénarios de prétraitement parallélisées où vos tâches en aval dépendent de la sortie de plusieurs tâches en amont.

## Un-à-un

Pour créer une tâche dépendant de l’exécution d’une autre tâche, vous devez spécifier un ID de tâche unique à la méthode statique [TaskDependencies][net_taskdependencies].[OnId][net_onid] lorsque vous renseignez la propriété [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

## Un-à-plusieurs

Pour créer une tâche dépendant de l’exécution de plusieurs tâches, vous devez spécifier un ensemble d’ID de tâche à la méthode statique [TaskDependencies][net_taskdependencies].[OnId][net_onids] lorsque vous renseignez la propriété [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

## Plage d’ID de tâche

Pour créer une tâche dépendant de l’exécution d’un groupe de tâches associées à des ID d’une plage spécifique, vous devez spécifier le premier ID et le dernier ID de la plage à la méthode statique [TaskDependencies][net_taskdependencies].[OnId][net_onidrange] lorsque vous renseignez la propriété [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Lorsque vous utilisez des plages d’ID de tâche pour vos dépendances, les ID de tâche de la plage *doivent* être des **représentations sous forme de chaîne** de **valeurs entières**. En outre, **chaque tâche de la plage** doit être correctement exécutée pour permettre l’exécution planifiée de la tâche dépendante.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## Exemple de code

L’exemple de projet [TaskDependencies][github_taskdependencies] est l’un des [exemples de code Azure Batch][github_samples] disponibles sur GitHub. Cette solution Visual Studio 2015 montre comment activer la dépendance de tâches sur un travail, créer des tâches qui dépendent d’autres tâches et exécuter ces tâches sur un pool de nœuds de calcul.

## Étapes suivantes

### Déploiement des applications

La fonctionnalité [packages d’application](batch-application-packages.md) de Batch est un moyen facile de déployer et contrôler les versions des applications exécutées par vos tâches sur des nœuds de calcul.

### Installation d’applications et de données intermédiaires

Pour découvrir les différentes méthodes de préparation des nœuds à l’exécution de tâches, consultez l’article [Installing applications and staging data on Batch compute nodes][forum_post] (Installation d’applications et de données intermédiaires sur les nœuds de calcul Batch) sur le forum Azure Batch. Rédigée par un membre de l’équipe Azure Batch, cette publication est une excellente introduction aux différentes façons d’obtenir des fichiers (y compris les applications et les données d’entrée de tâche) sur vos nœuds de calcul. Elle décrit également certains aspects à prendre en compte pour chaque méthode.

[forum_post]: https://social.msdn.microsoft.com/Forums/fr-FR/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Schéma : dépendance un-à-un"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Schéma : dépendance un-à-plusieurs"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Schéma : dépendance de plage d’ID de tâche"

<!---HONumber=AcomDC_0629_2016-->