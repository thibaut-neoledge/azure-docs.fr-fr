<properties
	pageTitle="Conservation des sorties des travaux et des tâches dans Azure Batch | Microsoft Azure"
	description="Découvrez comment utiliser Azure Storage comme banque de données durable pour les sorties des tâches et des travaux Batch, et comment activer l’affichage de ces sorties conservées dans le portail Azure."
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
	ms.date="08/06/2016"
	ms.author="marsma" />

# Conserver une sortie de tâche et de travail Azure Batch

Les tâches que vous exécutez dans Batch produisent généralement une sortie qui doit être stockée, puis récupérée ultérieurement par d’autres tâches dans le travail et/ou l’application cliente qui a exécuté le travail. Cette sortie peut correspondre aux fichiers créés par le traitement des données d’entrée ou aux fichiers journaux associés à l’exécution des tâches. Cet article présente une bibliothèque de classes .NET qui utilise une technique basée sur des conventions pour conserver une sortie de tâche de ce type dans le stockage d’objets blob Azure, la rendant ainsi disponible même après que vous avez supprimé vos pools, travaux et nœuds de calcul.

À l’aide de la technique présentée dans cet article, vous serez également en mesure d’afficher la sortie de votre tâche dans **Saved output files (Fichiers de sortie enregistrés)** et dans **Journaux enregistrés** dans le [portail Azure][portal].

![Sélecteurs Saved output files (Fichiers de sortie enregistrés) et Fichiers enregistrés dans le portail][1]

>[AZURE.NOTE] La méthode de stockage et de récupération des fichiers présentée ici fournit des fonctionnalités semblables à la façon dont **Batch Apps** (désormais obsolète) gérait les sorties des tâches.

## Considérations relatives aux sorties des tâches

Lorsque vous concevez votre solution Batch, vous devez prendre en compte les sorties des travaux et des tâches associées à plusieurs facteurs.

* **Durée de vie de nœud de calcul** : les nœuds de calcul sont souvent temporaires, notamment dans les pools à mise à l’échelle automatique. Les sorties des tâches qui s’exécutent sur un nœud sont disponibles uniquement lorsque le nœud existe, et seulement pendant la durée de rétention des fichiers que vous avez définie pour la tâche. Pour vous assurer de la conservation de la sortie de tâche, vos tâches doivent donc charger leurs fichiers de sortie vers une banque de données durable, par exemple, Azure Storage.

* **Stockage des sorties** : pour conserver les données de sortie de tâche dans un espace de stockage durable, vous pouvez utiliser le [Kit de développement logiciel (SDK) Azure Storage](../storage/storage-dotnet-how-to-use-blobs.md) dans votre code de tâche pour charger la sortie de tâche dans un conteneur de stockage d’objets blob. Si vous implémentez une convention d’affectation de noms de fichiers et de conteneurs, votre application cliente ou les autres tâches du travail peuvent alors rechercher et télécharger cette sortie en fonction de la convention.

* **Récupération de sortie** : vous pouvez récupérer une sortie de tâche directement à partir des nœuds de calcul de votre pool ou depuis Azure Storage si vos tâches conservent leur sortie. Pour récupérer la sortie d’une tâche directement à partir d’un nœud de calcul, vous avez besoin du nom de fichier et de son emplacement de sortie sur le nœud. Si vous conservez la sortie dans Azure Storage, les tâches en aval ou votre application cliente doivent disposer du chemin d’accès complet au fichier dans Azure Storage afin de le télécharger à l’aide du Kit de développement logiciel (SDK) Azure Storage.

* **Affichage de sortie** : lorsque vous accédez à une tâche Batch dans le portail Azure et sélectionnez **Files on node (Fichiers sur le nœud)**, tous les fichiers associés à la tâche s’affichent, et pas seulement le ou les fichiers de sortie qui vous intéressent. De nouveau, les fichiers hébergés sur les nœuds de calcul sont disponibles uniquement lorsque le nœud existe, et seulement pendant la durée de rétention des fichiers que vous avez définie pour la tâche. Pour afficher la sortie de tâche que vous avez conservée dans Azure Storage, dans le portail ou dans une application comme [Azure Storage Explorer][storage_explorer], vous devez connaître son emplacement et accéder directement au fichier.

## Aide relative à la conservation des sorties

Pour vous aider à conserver plus facilement les sorties des travaux et des tâches, l’équipe Batch a défini et implémenté un ensemble de conventions d’affectation de noms ainsi qu’une bibliothèque de classes .NET, la bibliothèque [Azure Batch File Conventions][nuget_package] que vous pouvez utiliser dans vos applications Batch. En outre, le portail Azure prend en charge ces conventions d’affectation de noms. Vous pouvez donc facilement rechercher les fichiers que vous avez stockés à l’aide de la bibliothèque.

## Utilisation de la bibliothèque de conventions de fichier

[Azure Batch File Conventions][nuget_package] est une bibliothèque de classes .NET que vos applications .NET Batch peuvent utiliser pour stocker et récupérer facilement les sorties des tâches vers et depuis Azure Storage. Cette bibliothèque est destinée à être utilisée dans le code de tâche et client ; dans le code de tâche pour la conservation des fichiers et dans le code client pour les répertorier et les récupérer. Les tâches peuvent également utiliser la bibliothèque pour récupérer les sorties des tâches en amont, comme dans un cas de [dépendances de tâche](batch-task-dependencies.md).

La bibliothèque de conventions permet de s’assurer que les conteneurs de stockage et les fichiers de sortie de tâche sont nommés conformément à la convention, et qu’ils sont chargés au bon emplacement lorsqu’ils sont conservés dans Azure Storage. Lorsque vous récupérez les sorties, vous pouvez localiser facilement les sorties d’une tâche ou d’un travail donné en répertoriant ou en récupérant les sorties par ID et par usage sans avoir à connaître les noms des fichiers ni leur emplacement dans Azure Storage.

Par exemple, vous pouvez utiliser la bibliothèque pour « répertorier tous les fichiers intermédiaires pour la tâche 7 » ou « obtenir la miniature de la tâche *mon\_film* » sans avoir à connaître les noms des fichiers ni leur emplacement dans votre compte Storage.

### Obtenir la bibliothèque

Vous pouvez obtenir la bibliothèque qui contient de nouvelles classes et étend les classes [CloudJob][net_cloudjob] et [CloudTask][net_cloudtask] avec de nouvelles méthodes à partir de [NuGet][nuget_package]. Vous pouvez l’ajouter à votre projet Visual Studio à l’aide du [Gestionnaire de package de bibliothèque NuGet][nuget_manager].

>[AZURE.TIP] Vous pouvez rechercher le [code source][github_file_conventions] de la bibliothèque Azure Batch File Conventions sur GitHub dans le Kit de développement logiciel (SDK) Microsoft Azure pour le référentiel .NET.

## Exigence : Compte de stockage lié

Pour stocker les sorties dans l’espace de stockage durable à l’aide de la bibliothèque de conventions de fichier et pour les afficher dans le portail Azure, vous devez [lier un compte Azure Storage](batch-application-packages.md#link-a-storage-account) à votre compte Batch. Si ce n’est déjà fait, liez un compte Storage à votre compte Batch à l’aide du portail Azure :

Panneau **Compte Batch** > **Paramètres** > **Compte de stockage** > **Compte de stockage** (Aucun) > Sélectionnez un compte Storage dans votre abonnement.

Pour une procédure pas à pas plus détaillée de la liaison d’un compte Storage, consultez l’article [Déploiement d’applications avec des packages d’applications Azure Batch](batch-application-packages.md).

## Conserver la sortie

Vous devez effectuer deux actions principales lors de l’enregistrement de la sortie de travail et de tâche avec la bibliothèque de conventions de fichier : créer le conteneur de stockage et y enregistrer la sortie.

>[AZURE.WARNING] Étant donné que toutes les sorties des travaux et des tâches sont stockées dans le même conteneur, des [limites de stockage](../storage/storage-performance-checklist.md#blobs) peuvent être appliquées si de très nombreuses tâches essaient de conserver des fichiers en même temps.

### Créer un conteneur de stockage

Avant que vos tâches ne commencent à conserver la sortie dans le stockage, vous devez créer un conteneur de stockage d’objets blob vers lequel elles chargeront leur sortie. Pour ce faire, appelez [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Cette méthode d’extension prend un objet [CloudStorageAccount][net_cloudstorageaccount] en tant que paramètre et crée un conteneur nommé de manière à ce que son contenu soit détectable par le portail Azure et par les méthodes de récupération présentées plus loin dans cet article.

En général, vous placez ce code dans votre application cliente, qui crée vos pools, travaux et tâches.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
	"myJob",
	new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
	new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### Stocker les sorties des tâches

Maintenant que vous avez préparé un conteneur dans le stockage d’objets blob, les tâches peuvent y enregistrer leur sortie à l’aide de la classe [TaskOutputStorage][net_taskoutputstorage] figurant dans la bibliothèque de conventions de fichier.

Dans votre code de tâche, commencez par créer un objet [TaskOutputStorage][net_taskoutputstorage], puis lorsque la tâche a terminé son travail, appelez la méthode [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] pour enregistrer sa sortie dans Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
	linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Le paramètre « output kind » catégorise les fichiers conservés. Il existe quatre types [TaskOutputKind][net_taskoutputkind] prédéfinis : « TaskOutput », « TaskPreview », « TaskLog » et « TaskIntermediate ». Vous pouvez également définir des types personnalisés s’ils peuvent être utiles dans votre flux de travail.

Ces types de sortie vous permettent de spécifier le type de sortie à répertorier lorsque vous interrogez ultérieurement Batch pour connaître les sorties conservées d’une tâche donnée. En d’autres termes, lorsque vous répertoriez les sorties d’une tâche, vous pouvez filtrer la liste sur l’un des types de sortie. Par exemple, « Donnez-moi un *aperçu* de la tâche *109* ». La section [Récupérer la sortie](#retrieve-output) plus loin dans l’article contient plus d’informations sur les listes de sorties et leur récupération.

>[AZURE.TIP] Le type de sortie indique également l’emplacement d’un fichier particulier dans le portail Azure : les fichiers catégorisés par *TaskOutput* sont affichés dans les fichiers de sortie de tâche tandis que les fichiers *TaskLog* sont affichés dans les journaux de tâches.

### Stocker les sorties des travaux

Outre le stockage des sorties des tâches, vous pouvez stocker les sorties associées à un travail entier. Par exemple, dans la tâche de fusion d’un travail de rendu de film, vous pouvez conserver le film intégralement rendu sous forme de sortie de travail. Lorsque votre travail est terminé, votre application cliente peut simplement répertorier et récupérer les sorties du travail, sans devoir interroger les tâches individuelles.

Stockez la sortie du travail en appelant la méthode [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] et spécifiez le paramètre [JobOutputKind][net_joboutputkind] et le nom de fichier :

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Comme avec le paramètre TaskOutputKind pour les sorties des tâches, vous pouvez utiliser le paramètre [JobOutputKind][net_joboutputkind] pour catégoriser les fichiers conservés d’un travail. Cela vous permet d’interroger (répertorier) ultérieurement un type spécifique de sortie. Le paramètre JobOutputKind inclut les types de sortie et d’aperçu, et prend en charge la création de types personnalisés.

### Stocker les journaux de tâches

En plus de conserver un fichier dans un espace de stockage durable à l’issue d’une tâche ou d’un travail, vous pouvez juger nécessaire de conserver les fichiers mis à jour pendant l’exécution d’une tâche, par exemple les fichiers journaux ou `stdout.txt` et `stderr.txt`. À cet effet, la bibliothèque Azure Batch File Conventions fournit la méthode [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]. Avec [SaveTrackedAsync][net_savetrackedasync], vous pouvez effectuer le suivi des mises à jour apportées à un fichier sur le nœud (à un intervalle que vous définissez) et conserver ces mises à jour dans Azure Storage.

Dans l’extrait de code suivant, nous utilisons [SaveTrackedAsync][net_savetrackedasync] pour mettre à jour `stdout.txt` dans Azure Storage toutes les 15 secondes pendant l’exécution de la tâche :

```csharp
string logFilePath = Path.Combine(
	Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

using (ITrackedSaveOperation stdout =
		taskStorage.SaveTrackedAsync(
		TaskOutputKind.TaskLog,
		logFilePath,
		"stdout.txt",
		TimeSpan.FromSeconds(15)))
{
	/* Code to process data and produce output file(s) */
}
```

`Code to process data and produce output file(s)` est simplement un espace réservé pour le code que votre tâche exécuterait normalement. Par exemple, vous pouvez insérer un code qui télécharge des données à partir d’Azure Storage et effectue sur celles-ci des transformations ou des calculs. La partie importante de cet extrait de code montre comment vous pouvez encapsuler un tel code dans un bloc `using` pour mettre régulièrement à jour un fichier avec [SaveTrackedAsync][net_savetrackedasync].

>[AZURE.NOTE] Lorsque vous activez le suivi des fichiers avec SaveTrackedAsync, seuls les *ajouts* apportés au fichier suivi sont conservés dans Azure Storage. Vous devez utiliser cette méthode uniquement pour le suivi des fichiers journaux sans rotation ou des autres fichiers qui leur sont ajoutés. Autrement dit, les données sont uniquement ajoutées à la fin du fichier lorsqu’il est mis à jour.

## Récupérer la sortie

Lorsque vous récupérez votre sortie conservée à l’aide de la bibliothèque Azure Batch File Conventions, vous procédez d’une façon centrée sur les travaux et les tâches. Vous pouvez demander la sortie d’une tâche ou d’un travail donné sans avoir à connaître son chemin d’accès dans le stockage d’objets blob ni même son nom de fichier. Vous pouvez dire simplement, « Indiquez-moi les fichiers de sortie de la tâche *109* ».

L’extrait de code indiqué ci-dessous itère toutes les tâches d’un travail, imprime des informations sur les fichiers de sortie de la tâche, puis télécharge les fichiers à partir d’Azure Storage.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
		task.OutputStorage(storageAccount).ListOutputs(
			TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

		output.DownloadToFileAsync(
			$"{jobId}-{output.FilePath}",
			System.IO.FileMode.Create).Wait();
    }
}
```

## Sorties des tâches et portail Azure

Le portail Azure affiche les sorties des tâches et les journaux qui sont conservés dans un compte Azure Storage lié à l’aide des conventions d’affectation de noms du fichier [LISEZMOI d’Azure Batch File Conventions][github_file_conventions_readme]. Vous pouvez implémenter ces conventions vous-même dans le langage de votre choix ou utiliser la bibliothèque de conventions de fichier dans vos applications .NET.

### Activer l’affichage du portail

Pour activer l’affichage de vos sorties dans le portail, vous devez respecter les exigences suivantes :

 1. [Liez un compte Azure Storage](#requirement-linked-storage-account) à votre compte Batch.
 2. Respectez les conventions d’affectation de noms prédéfinies pour les conteneurs de stockage et les fichiers lors de la conservation des sorties. Vous trouverez la définition de ces conventions dans le fichier [LISEZMOI][github_file_conventions_readme] de la bibliothèque de conventions de fichier. Si vous utilisez la bibliothèque [Azure Batch File Conventions][nuget_package] pour conserver votre sortie, cette exigence est satisfaite.

### Afficher les sorties dans le portail

Pour afficher les sorties des tâches et les journaux dans le portail Azure, accédez à la tâche dont la sortie vous intéresse, puis cliquez sur **Saved output files (Fichiers de sortie enregistrés)** ou **Journaux enregistrés**. Cette image affiche l’écran **Saved output files (Fichiers de sortie enregistrés)** pour la tâche pourvue de l’ID « 007 » :

![Panneau des sorties des tâches dans le portail Azure][2]

## Exemple de code

L’exemple de projet [PersistOutputs][github_persistoutputs] est l’un des [exemples de code Azure Batch][github_samples] disponibles sur GitHub. Cette solution Visual Studio 2015 montre comment utiliser la bibliothèque Azure Batch File Conventions pour conserver une sortie de tâche dans l’espace de stockage durable. Pour exécuter l’exemple, procédez comme suit :

1. Ouvrez le projet dans **Visual Studio 2015**.
2. Ajoutez vos **informations d’identification de compte** Batch et Storage à **AccountSettings.settings** dans le projet Microsoft.Azure.Batch.Samples.Common.
3. **Générez** la solution sans l’exécuter. Restaurez les packages NuGet si vous y êtes invité.
4. Utilisez le portail Azure pour charger un [package d’application](batch-application-packages.md) pour **PersistOutputsTask**. Insérez le fichier `PersistOutputsTask.exe` et ses assemblys dépendants dans le package .zip, puis définissez l’ID de l’application sur PersistOutputsTask et la version du package d’application sur 1.0.
5. **Démarrez** (exécutez) le projet **PersistOutputs**.

## Étapes suivantes

### Déploiement des applications

La fonctionnalité [packages d’application](batch-application-packages.md) de Batch permet de déployer et de contrôler facilement les versions des applications exécutées par vos tâches sur des nœuds de calcul.

### Installation d’applications et de données intermédiaires

Pour découvrir les différentes méthodes de préparation des nœuds à l’exécution de tâches, consultez l’article [Installing applications and staging data on Batch compute nodes][forum_post] \(Installation d’applications et de données intermédiaires sur les nœuds de calcul Batch) sur le forum Azure Batch. Rédigée par un membre de l’équipe Azure Batch, cette publication est une excellente introduction aux différentes façons d’obtenir des fichiers (y compris les applications et les données d’entrée de tâche) sur vos nœuds de calcul. Elle décrit également certains aspects à prendre en compte pour chaque méthode.

[forum_post]: https://social.msdn.microsoft.com/Forums/fr-FR/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Sélecteurs Saved output files (Fichiers de sortie enregistrés) et Fichiers enregistrés dans le portail"
[2]: ./media/batch-task-output/task-output-02.png "Panneau des sorties des tâches dans le portail Azure"

<!---HONumber=AcomDC_0810_2016-->