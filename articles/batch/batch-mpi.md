<properties
	pageTitle="Exécuter des applications MPI dans Azure Batch avec des tâches multi-instances | Microsoft Azure"
	description="Découvrez comment exécuter des applications MPI (Message Passing Interface) en utilisant des tâches de type multi-instances dans Azure Batch."
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
	ms.date="02/19/2016"
	ms.author="marsma" />

# Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch

Avec les tâches multi-instances, vous pouvez exécuter une tâche Azure Batch sur plusieurs nœuds de calcul simultanément pour permettre des scénarios de calcul haute performance comme les applications MPI (Message Passing Interface). Dans cet article, vous allez apprendre à exécuter des tâches multi-instances à l’aide de la bibliothèque [Batch.NET][api_net].

## Présentation des tâches multi-instances

Dans Azure Batch, chaque tâche est normalement exécutée sur un nœud de calcul unique : vous soumettez plusieurs tâches à un travail, et le service Azure Batch planifie l’exécution de chacune d’entre elles sur un nœud. Toutefois, en configurant les **paramètres multi-instances** d’une tâche, vous pouvez ordonner à Azure Batch de fractionner cette tâche en tâches subordonnées pour une exécution sur plusieurs nœuds.

![Présentation des tâches multi-instances][1]

Lorsque vous envoyez à un travail une tâche dotée de paramètres multi-instances, Azure Batch exécute plusieurs étapes uniques aux tâches multi-instances :

1. Le service Azure Batch fractionne automatiquement la tâche en une tâche **principale** et en plusieurs **tâches subordonnées**. Azure Batch planifie ensuite l’exécution de la tâche principale et des tâches subordonnées sur les nœuds de calcul du pool.
2. Ces tâches, tant la tâche principale que les tâches subordonnées, téléchargent les **fichiers de ressources communs** que vous indiquez dans les paramètres multi-instances.
3. Une fois les fichiers de ressources communs téléchargés, la tâche principale et les tâches subordonnées exécutent la **commande de coordination** que vous indiquez dans les paramètres multi-instances. Cette commande de coordination lance habituellement un service à l’arrière-plan (comme le fichier `smpd.exe` de l’application [Microsoft MPI][msmpi_msdn]), et vérifie également que les nœuds sont prêts à traiter les messages entre les nœuds.
4. Au moment où la tâche principale et toutes les tâches subordonnées finissent d’exécuter la commande de coordination, l’exécution de la **ligne de commande** de la tâche multi-instances (la « commande d’application ») intervient, *uniquement* par la **tâche principale**. Par exemple, dans une solution basée sur [MS-MPI][msmpi_msdn], c’est là où vous exécutez votre application prenant en charge MPI au moyen du fichier `mpiexec.exe`.

> [AZURE.NOTE] Même si elle est différente d’un point de vue fonctionnel, la « tâche multi-instances » n’est pas un type de tâche unique comme [StartTask][net_starttask] ou [JobPreparationTask][net_jobprep]. La tâche multi-instances est simplement une tâche Azure Batch standard ([CloudTask][net_task] dans Batch .NET) dont les paramètres multi-instances ont été configurés. Dans cet article, nous parlons de **tâche multi-instances**.

## Configuration requise pour les tâches multi-instances

Les tâches multi-instances nécessitent un pool avec **communication entre les nœuds activée** et **exécution de tâches simultanées désactivée**. Si vous essayez d’exécuter une tâche multi-instances dans un pool dont la communication entre les nœuds a été désactivée ou avec une valeur *maxTasksPerNode* supérieure à 1, la tâche ne sera jamais planifiée : elle restera indéfiniment à l’état « actif ». Cet extrait de code illustre la création d’un tel pool à l’aide de la bibliothèque Batch.NET.

```
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		osFamily: "4",
		virtualMachineSize: "small",
		targetDedicated: 3);

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

En outre, les tâches multi-instances s’exécutent *uniquement* sur les nœuds des **pools créés après le 14 décembre 2015**.

> [AZURE.TIP] Lorsque vous utilisez des [nœuds de calcul de taille A8 ou A9](./../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) dans le pool Azure Batch, votre application MPI peut tirer parti du réseau d’accès direct à la mémoire à distance (RDMA) haute performance d’Azure. Vous pouvez voir la liste complète des tailles de nœud de calcul disponibles pour les pools Azure Batch dans [Tailles de services cloud](./../cloud-services/cloud-services-sizes-specs.md).

### Utiliser une tâche StartTask pour l’installation de l’application MPI

Pour exécuter des applications MPI avec une tâche multi-instances, vous devez d’abord obtenir votre logiciel MPI sur les nœuds de calcul du pool. C’est là l’occasion idéale d’utiliser une tâche [StartTask][net_starttask] qui s’exécute à chaque fois qu’un nœud rejoint un pool ou est redémarré. Cet extrait de code crée une tâche StartTask qui spécifie le package d’installation de MS-MPI comme un [fichier de ressources][net_resourcefile], mais aussi la ligne de commande qui va être exécutée une fois le fichier de ressources téléchargé sur le nœud.

```
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] Lors de l’implémentation d’une solution MPI avec des tâches multi-instances dans Azure Batch, vous n’êtes pas limité à l’utilisation de MS-MPI. Vous pouvez utiliser n’importe quelle implémentation standard de l’application MPI, dès lors qu’elle est compatible avec le système d’exploitation indiqué pour les nœuds de calcul du pool.

## Créer une tâche multi-instances avec Batch.NET

Maintenant que nous avons abordé les exigences du pool et l’installation du package MPI, nous allons créer la tâche multi-instances. Dans cet extrait de code, nous créons une norme [CloudTask][net_task], puis nous configurons sa propriété [MultiInstanceSettings][net_multiinstance_prop]. Comme mentionné ci-dessus, la tâche multi-instances n’est pas un type de tâche distinct, mais une tâche Azure Batch standard configurée avec des paramètres multi-instances.

```
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## Tâche principale et tâches subordonnées

Lorsque vous créez les paramètres multi-instances d’une tâche, vous indiquez le nombre de nœuds de calcul qui vont exécuter la tâche. Lorsque vous soumettez la tâche à un travail, le service Azure Batch crée une tâche **principale** et suffisamment de **tâches subordonnées** pour le nombre de nœuds indiqué.

Ces tâches se voient affecter un identifiant entier allant de 0 à *numberOfInstances - 1*. La tâche dont l’identifiant est 0 est la tâche principale, tandis que tous les autres identifiants correspondent aux tâches subordonnées. Par exemple, si vous créez les paramètres multi-instances suivants d’une tâche, la tâche principale a pour identifiant 0 et les tâches subordonnées, des identifiants allant de 1 à 9.

```
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## Commandes de coordination et d’application

La **commande de coordination** est exécutée à la fois par la tâche principale et les tâches subordonnées. Une fois que la tâche principale et toutes les tâches subordonnées ont terminé d’exécuter la commande de coordination, la ligne de commande de la tâche multi-instances est exécutée par la tâche principale *uniquement*. Nous appelons cette ligne de commande la **commande d’application** pour la différencier de la commande de coordination.

L’appel de la commande de coordination bloque : Azure Batch exécute uniquement la commande d’application lorsque la commande de coordination a été renvoyée avec succès pour toutes les tâches subordonnées. Par conséquent, la commande de coordination doit démarrer tous les services d’arrière-plan requis, vérifier qu’ils sont prêts à être utilisés, puis se fermer. Par exemple, cette commande coordination pour une solution qui utilise la version 7 de MS-MPI démarre le service SMPD sur le nœud, puis se ferme :

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Notez l’utilisation de `start` dans cette commande de coordination. Cela est nécessaire, car l’application `smpd.exe` n’est pas immédiatement renvoyée après l’exécution. Sans l’utilisation de la commande [start][cmd_start], cette commande de coordination ne serait pas renvoyée et bloquerait par conséquent l’exécution de la commande d’application.

La **commande d’application**, la ligne de commande indiquée pour la tâche multi-instances, est exécutée *uniquement* par la tâche principale. Pour les applications MS-MPI, il s’agit d’exécuter votre application prenant en charge MPI à l’aide de `mpiexec.exe`. Par exemple, voici une commande d’application pour une solution qui utilise la version 7 de MS-MPI :

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

## Fichiers de ressources

Il existe deux ensembles de fichiers de ressources à prendre en compte pour les tâches multi-instances : les **fichiers de ressources communs** que *toutes* les tâches téléchargent (tâche principale et tâches subordonnées) et les **fichiers de ressources** indiqués pour la tâche multi-instances elle-même que *seule la tâche principale* télécharge.

Vous pouvez indiquer un ou plusieurs **fichiers de ressources communs** dans les paramètres multi-instances d’une tâche. Ces fichiers de ressources communs sont téléchargés à partir de [Azure Storage](./../storage/storage-introduction.md) dans le répertoire partagé de tâche de chaque nœud, par la tâche principale et les tâches subordonnées. Vous pouvez accéder au répertoire partagé de la tâche à partir de l’application et des lignes de commande de coordination à l’aide de la variable d’environnement `AZ_BATCH_TASK_SHARED_DIR`.

Les fichiers de ressources que vous indiquez dans la tâche multi-instances elle-même sont téléchargés dans le répertoire de travail de la tâche `AZ_BATCH_TASK_WORKING_DIR`, par la tâche principale *uniquement* : les tâches subordonnées ne téléchargent pas les fichiers de ressources indiqués par la tâche multi-instances.

Le contenu du répertoire `AZ_BATCH_TASK_SHARED_DIR` est accessible par la tâche principale et toutes les tâches subordonnées qui s’exécutent sur un nœud. `tasks/mybatchjob/job-1/mymultiinstancetask/` est un exemple de répertoire partagé de tâche. La tâche principale et chaque tâche subordonnée possèdent également un répertoire de travail accessible par cette tâche uniquement, à l’aide de la variable d’environnement `AZ_BATCH_TASK_WORKING_DIR`.

Notez que dans les exemples de code de cet article, nous n’indiquons pas les fichiers de ressources de la tâche multi-instances elle-même. Nous les indiquons uniquement pour la tâche StartTask du pool et la tâche [CommonResourceFiles][net_multiinsance_commonresfiles] des paramètres multi-instances.

> [AZURE.IMPORTANT] Veillez à toujours utiliser les variables d’environnement `AZ_BATCH_TASK_SHARED_DIR` et `AZ_BATCH_TASK_WORKING_DIR` pour faire référence à ces répertoires dans les lignes de commande. N’essayez pas de construire les chemins d’accès manuellement.

## Durée de vie de la tâche

La durée de vie de la tâche principale contrôle la durée de vie de la tâche multi-instances tout entière. Lorsque la tâche principale s’arrête, toutes les tâches subordonnées s’arrêtent aussi. Le code de sortie de la tâche principale est le code de sortie de la tâche. Il est donc utilisé pour déterminer la réussite ou l’échec de la tâche pour toutes nouvelles tentatives.

Si l’une des tâches subordonnées échoue, par exemple en se fermant avec un code de retour différent de zéro, la tâche multi-instances tout entière échoue. La tâche multi-instances s’arrête ensuite puis reprend, dans la limite du nombre de nouvelles tentatives défini.

Quand vous supprimez une tâche multi-instances, la tâche principale et toutes les tâches subordonnées sont également supprimées par le service Azure Batch. Tous les répertoires des tâches subordonnées et leurs fichiers sont supprimés des nœuds de calcul, à l’image d’une tâche standard.

[TaskConstraints][net_taskconstraints] pour une tâche multi-instances, par exemple les propriétés [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] et [RetentionTime][net_taskconstraint_retention], sont honorées en l’état pour une tâche standard, puis appliquer à la tâche principale et à toutes les tâches subordonnées. Toutefois, si vous modifiez la propriété [RetentionTime][net_taskconstraint_retention] après l’ajout de la tâche multi-instances au travail, cette modification s’applique uniquement à la tâche principale. Toutes les tâches subordonnées vont continuer à utiliser la propriété [RetentionTime][net_taskconstraint_retention] d’origine.

Une liste des tâches récentes d’un nœud de calcul reflète l’identifiant d’une tâche subordonnée si la tâche récente faisait partie d’une tâche multi-instances.

## Obtenir des informations sur les tâches subordonnées

Pour obtenir plus d’informations sur les tâches subordonnées à l’aide de la bibliothèque Batch.NET, appelez la méthode [CloudTask.ListSubtasks][net_task_listsubtasks]. Cette méthode renvoie des informations sur toutes les tâches subordonnées, ainsi que sur le nœud de calcul qui a exécuté les tâches. À partir de ces informations, vous pouvez déterminer le répertoire racine de chaque tâche subordonnée, l’identifiant du pool, son état actuel, le code de sortie, etc. Utilisez ces informations en même temps que la méthode [PoolOperations.GetNodeFile][poolops_getnodefile] pour obtenir les fichiers de la tâche subordonnée. Notez que cette méthode ne renvoie pas d’informations pour la tâche principale (identifiant 0).

> [AZURE.NOTE] Sauf indication contraire, les méthodes Batch.NET qui interviennent sur la tâche multi-instances [CloudTask][net_task] elle-même s’appliquent *uniquement* à la tâche principale. Par exemple, lorsque vous appelez la méthode [CloudTask.ListNodeFiles][net_task_listnodefiles] sur une tâche multi-instances, seuls les fichiers de la tâche principale sont renvoyés.

L’extrait de code suivant montre comment obtenir des informations sur les tâches subordonnées et comment demander le contenu du fichier à partir des nœuds sur lesquels elles sont exécutées.

```
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## Étapes suivantes

- Vous voulez peut-être créer une application MS-MPI simple à utiliser tout en testant des tâches multi-instances dans Azure Batch. L’article de blog Microsoft HPC et Azure Batch [How to compile and run a simple MS-MPI program][msmpi_howto] (Comment compiler et exécuter un programme MS-MPI simple) contient une procédure pas à pas pour créer une application MPI simple à l’aide de MS-MPI.

- Découvrez la page [Microsoft MPI][msmpi_msdn] sur MSDN pour en savoir plus sur MS-MPI.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Présentation multi-instances"

<!---HONumber=AcomDC_0224_2016-->