<properties
	pageTitle="Préparation et nettoyage du travail dans Batch | Microsoft Azure"
	description="Utilisez des tâches de préparation au niveau du travail afin de réduire le transfert de données vers les nœuds de calcul Azure Batch et des tâches de validation pour le nettoyage du nœud à l'achèvement du travail."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/15/2015"
	ms.author="v-marsma"/>

# Exécution de tâches de préparation et de fin du travail sur les nœuds de calcul Azure Batch

Les travaux Azure Batch nécessitent souvent une forme d'installation avant leur exécution, ainsi qu'une sorte de maintenance ultérieure une fois que les tâches du travail sont terminées. Batch fournit les mécanismes de cette préparation et cette maintenance sous la forme de tâches de *préparation du travail* et *validation du travail* facultatives.

La tâche de préparation du travail s'exécute sur tous les nœuds de calcul prévus pour exécuter les tâches, avant que les autres tâches du travail ne soient exécutées. Lorsqu'un travail est terminé, la tâche de validation du travail s'exécute sur chaque nœud dans le pool ayant exécuté au moins une tâche. Pour les tâches de préparation et de validation du travail, vous pouvez spécifier une ligne de commande à exécuter lorsque la tâche est appelée. Les tâches offrent des fonctionnalités telles que le téléchargement de fichiers, une exécution élevée, des variables d'environnement personnalisées, une durée d'exécution maximale, le nombre de tentatives et la période de rétention des fichiers.

Dans les sections suivantes, vous découvrirez comment utiliser ces deux types de tâches spécifiques à l'aide de la classe [JobPreparationTask][net_job_prep] et de la classe [JobReleaseTask][net_job_release] dans l'API [.NET Batch][api_net].

> [AZURE.TIP]Les tâches de préparation et de validation du travail sont particulièrement utiles dans les environnements de «pool partagé » ; ces environnements dans lesquels un pool de nœuds de calcul persiste entre les exécutions d'un travail et est partagé entre plusieurs travaux différents.

## Utilisation des tâches de préparation et de validation du travail

Un certain nombre de situations bénéficient des tâches de préparation et de validation du travail. En voici quelques-unes :

- **Transfert de données de tâches communes** : les travaux Batch nécessitent souvent un ensemble commun de données comme entrée pour les tâches du travail. Par exemple, dans les calculs quotidiens de l'analyse des risques, les données de marché sont spécifiques à un travail, mais communes à toutes les tâches du travail. Ces données de marché, souvent d'une taille de plusieurs gigaoctets, ne doivent être téléchargées qu'une seule fois sur chaque nœud de calcul afin que chaque tâche qui s'exécute sur un nœud puisse les utiliser. Utilisez une *tâche de préparation du travail* pour télécharger les données sur chaque nœud avant l'exécution d'autres tâches.
- **Suppression des données du travail** : dans un environnement de pool partagé dans lequel les nœuds de calcul d'un pool ne sont pas arrêtés entre les travaux, la suppression des données du travail entre les exécutions peut être nécessaire pour économiser de l'espace disque sur les nœuds ou pour respecter les stratégies de sécurité d'une organisation. Utilisez une *tâche de validation du travail* pour supprimer les données téléchargées par une tâche de préparation du travail ou générées pendant l'exécution d'une tâche.
- **Rétention des journaux** : vous voulez peut-être conserver une copie des fichiers journaux générés par les tâches ou peut-être les fichiers de vidage sur incident qui peuvent être générés par les applications ayant échoué. Dans ces cas, utilisez une *tâche de validation du travail* pour compresser et télécharger ces données vers un compte [Azure Storage][azure_storage].

## Tâche de préparation du travail

Avant l'exécution des tâches d'un travail, la tâche de préparation du travail est exécutée sur chaque nœud de calcul où l'exécution d'une tâche est planifiée. Par défaut, le service Batch attend la fin de la tâche de préparation du travail avant d'exécuter les tâches planifiées sur le nœud. Toutefois, vous pouvez configurer le service pour qu'il n'attende pas. La tâche de préparation du travail s'exécutera à nouveau sur un nœud de calcul si le nœud redémarre, mais vous pouvez également désactiver ce comportement.

La tâche de préparation du travail est exécutée uniquement sur les nœuds où l'exécution d'une tâche est planifiée. Ceci empêche l'exécution d'une tâche de préparation inutile dans le cas où une tâche n'est pas attribuée à un nœud. Cela permet de réduire les coûts de transfert de données, par exemple. Cette situation s'applique lorsque le nombre de tâches pour un travail est inférieur au nombre de nœuds dans un pool. Elle s'applique également si l'[exécution de tâches simultanées](batch-parallel-node-tasks.md) est activée. Dans ce cas, certains nœuds restent inactifs si le nombre de tâches est inférieur au nombre total de tâches simultanées possibles.

> [AZURE.NOTE] [JobPreparationTask]La tâche [net\_job\_prep\_cloudjob] diffère de la tâche [CloudPool.StartTask][pool_starttask] dans la mesure où JobPreparationTask s'exécute au début de chaque tâche, tandis que StartTask s'exécute uniquement lorsqu'un nœud de calcul rejoint un pool ou redémarre.

## Tâche de validation du travail

Lorsqu'un travail est terminé, la tâche de validation du travail s'exécute sur chaque nœud dans le pool ayant exécuté au moins une tâche. Vous marquez un travail comme terminé en émettant une requête de fin. Ensuite, le service Batch définit l'état du travail sur *arrêt*, met fin à toutes les tâches actives ou en cours d'exécution associées au travail et exécute la tâche de validation du travail. Le travail passe ensuite à l'état *terminé*.

> [AZURE.NOTE]La suppression du travail exécute également la tâche de validation du travail. Toutefois, si un travail a été arrêté précédemment, la tâche de validation n'est pas exécutée une deuxième fois lorsque ce travail est supprimé.

## Tâches de préparation et de validation du travail dans l'API .NET Batch

Pour spécifier une tâche de préparation du travail, vous créez et configurez l'objet [JobPreparationTask][net_job_prep] et vous l'attribuez à la propriété [CloudJob.JobPreparationTask][net_job_prep_cloudjob] de votre travail. De même, initialisez la propriété [JobReleaseTask][net_job_release] et attribuez-la à la propriété [CloudJob.JobReleaseTask][net_job_prep_cloudjob] de votre travail pour définir la tâche de validation du travail.

Dans cet extrait de code, `myBatchClient` est une instance complètement initialisée de [BatchClient][net_batch_client] et `myPool` est un pool existant dans le compte Batch.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

Comme mentionné ci-dessus, la tâche de validation est exécutée lorsqu'un travail est terminé ou supprimé. La fin d'un travail avec l'API .NET Batch s'effectue en appelant [PoolOperations.TerminateJobAsync][net_job_terminate]. La suppression d'un travail s'exécute avec [PoolOperations.DeleteJobAsync][net_job_delete]. Ces deux actions sont généralement exécutées lorsque les tâches d'un travail sont terminées ou lorsqu'un délai d'attente que vous avez défini a été atteint.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Étapes suivantes

### Exemple de projet sur GitHub

Découvrez l'exemple de projet [JobPrepRelease][job_prep_release_sample] sur GitHub pour voir les tâches de préparation et de validation du travail en action. Cette application de console effectue les opérations suivantes :

1. Crée un pool avec deux « petits » nœuds.
2. Crée un travail avec des tâches de préparation du travail, de validation et standard.
3. Exécute la tâche de préparation du travail qui écrit d'abord l'ID de nœud dans un fichier texte dans le répertoire « partagé » d'un nœud.
4. Exécute une tâche sur chaque nœud qui écrit son ID de tâche dans le même fichier texte.
5. Lorsque toutes les tâches sont terminées (ou que le délai d'attente est atteint), imprime le contenu du fichier texte de chaque nœud dans la console.
6. Lorsque le travail est terminé, exécute la tâche de validation du travail pour supprimer le fichier du nœud.
6. Imprime les codes de sortie des tâches de préparation et de validation du travail pour chaque nœud sur lequel elles sont exécutées.
7. Interrompt l'exécution pour permettre la confirmation de la suppression du pool et/ou du travail.

Le résultat de l'exemple d'application ressemble à ce qui suit :

```
Attempting to create pool: JobPrepReleaseSamplePool
The pool already existed when we tried to create it
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-3105992504_1-20151015t150030z:
-------------------------------------------
tvm-3105992504_1-20151015t150030z tasks:
  task001
  task002
  task006
  task007

Contents of shared\job_prep_and_release.txt on tvm-3105992504_2-20151015t150030z:
-------------------------------------------
tvm-3105992504_2-20151015t150030z tasks:
  task003
  task005
  task004
  task008

Waiting for job JobPrepReleaseSampleJob to reach state Completed
....

tvm-3105992504_1-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-3105992504_2-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
no

Sample complete, hit ENTER to exit...
```

### Inspection des tâches de préparation et de validation du travail avec l'Explorateur Batch

L'[Explorateur Batch][batch_explorer_article], également situé dans le [référentiel d'exemple de code][batch_explorer_project] Batch sur GitHub, est un excellent outil pour développer des solutions avec Azure Batch. Lorsque vous exécutez l'exemple d'application ci-dessus, par exemple, vous pouvez utiliser l'Explorateur Batch pour afficher les propriétés du travail et ses tâches ou même télécharger le fichier texte partagé modifié par les tâches du travail.

La capture d'écran ci-dessous met en évidence les propriétés des tâches de préparation et de validation du travail affichées dans le panneau **Détails de la tâche** lorsque vous sélectionnez le travail *JobPrepReleaseSampleJob* dans l'onglet **Travaux**.

![Explorateur Batch][1]

*Capture d'écran de l'Explorateur Batch affichant les tâches de préparation et de validation du travail*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_article]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/batchexplorer-01.png
[2]: ./media/batch-job-prep-release/batchexplorer-02.png

<!---HONumber=AcomDC_0114_2016-->