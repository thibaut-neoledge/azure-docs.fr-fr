<properties
	pageTitle="Didacticiel - Prise en main de la bibliothèque .NET Azure Batch"
	description="Découvrez les concepts de base d'Azure Batch et comment développer à l’aide du service Batch avec un scénario simple"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>

# Prise en main de la bibliothèque Azure Batch pour .NET  

Ce didacticiel vous explique comment créer une application console qui configure un programme et des fichiers de prise en charge s’exécutant sur plusieurs nœuds de calcul dans un pool Azure Batch. Les tâches créées dans ce didacticiel évaluent le texte à partir de fichiers du stockage Azure et renvoient les mots les plus couramment utilisés. Les exemples ont été écrits en code C# et utilisent la bibliothèque .NET Azure Batch.

## Composants requis

- Comptes :

	- **Compte Azure** : vous pouvez créer un compte d’essai gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

	- **Compte Batch** : voir la section **Compte Batch** de l’article [Vue d’ensemble technique d’Azure Batch](batch-technical-overview.md).

	- **Compte de stockage** : voir la section **Créez un compte de stockage** de l’article [À propos des comptes de stockage Azure](../storage-create-storage-account.md). Dans ce didacticiel, vous créez un conteneur dans ce compte nommé **testcon1**.

- Projet d’application console Visual Studio :

	1.  Après avoir ouvert Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

	2.	À partir de **Windows**, sous **Visual C#**, cliquez sur **Application console**, nommez le projet **GettingStarted**, nommez la solution **AzureBatch**, puis cliquez sur **OK**.

- Assemblys NuGet :

	1. Après avoir créé votre projet dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et sélectionnez **Gérer les package NuGet**. Recherchez en ligne **Azure.Batch**, puis cliquez sur **Installer** pour installer le package Microsoft Azure Batch et ses dépendances.

	2. Recherchez en ligne **WindowsAzure.Storage**, puis cliquez sur **Installer** pour installer le package Azure Storage et ses dépendances.

## Étape 1 : créer et charger les fichiers de prise en charge

Pour la prise en charge de l’application, un conteneur est créé dans Azure Storage, les fichiers texte sont créés, puis les fichiers texte et les fichiers de prise en charge sont chargés dans le conteneur.

### Configurer la chaîne de connexion de stockage

1. Ouvrez le fichier App.config du projet GettingStarted, puis ajoutez l’élément &lt;appSettings&gt; à &lt;configuration&gt;.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Remplacez les valeurs suivantes :

	- **[account-name]** : nom du compte de stockage que vous avez créé précédemment.

	- **[account-key]** : clé primaire du compte de stockage. Vous pouvez trouver la clé primaire dans la page Stockage du Portail de gestion.

2. Enregistrez le fichier App.config.

Pour plus d’informations, voir [Configuration de chaînes de connexion](http://msdn.microsoft.com/library/windowsazure/ee758697.aspx).

### Créer le conteneur de stockage

1. Ajoutez les déclarations d’espace de noms ci-après en haut du fichier Program.cs du projet GettingStarted :

		using System.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Ajoutez à la classe Program la méthode ci-dessous qui obtient la chaîne de connexion de stockage, crée le conteneur et définit les autorisations :

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

3. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		CreateStorage();

4. Enregistrez le fichier Program.cs.

	> [AZURE.NOTE]Dans un environnement de production, il est recommandé d'utiliser une signature d'accès partagé.

Pour plus d’informations, voir l’article [Utilisation du stockage d’objets blob à partir de .NET](../storage-dotnet-how-to-use-blobs.md).

### Créer le programme de traitement

1. Dans l’Explorateur de solutions, créez un projet d’application console nommé **ProcessTaskData**.

2. Ajoutez à la section Main le code ci-après qui traite le texte des fichiers :

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
		string[] words = content.Split(' ');
		var topNWords =
		  words.
			Where(word => word.Length > 0).
			GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
			OrderByDescending(x => x.Value).
			Take(numTopN).
			ToList();

		foreach (var pair in topNWords)
		{
			Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

3. Enregistrez et générez le projet ProcessTaskData.

### Créer les fichiers de données

1. Dans le projet GettingStarted, créez un fichier texte nommé **taskdata1**, copiez-y le texte suivant, puis enregistrez le fichier.

	Quand vous avez besoin de ressources flexibles qui répondent aux besoins de votre entreprise, vous pouvez utiliser des machines virtuelles Azure pour fournir une infrastructure de calcul à la demande et évolutive. Dans la galerie, vous pouvez créer des machines virtuelles qui exécutent Windows, Linux et des applications d'entreprise comme SQL Server et SharePoint. Vous pouvez également capturer et utiliser vos propres images pour créer des machines virtuelles personnalisées.

2. Créez un fichier texte nommé **taskdata2**, copiez-y le texte suivant, puis enregistrez le fichier.

	Déployez et gérez rapidement des applications et des services puissants via Azure Cloud Services. Téléchargez simplement votre application. Azure gère les détails du déploiement (de l'approvisionnement et de l'équilibrage de charge à l'analyse du fonctionnement pour une disponibilité continue). Votre application est prise en charge dans le cadre d'un contrat SLA mensuel renommé qui prévoit une disponibilité à 99,95 %. Vous vous concentrez uniquement sur l'application, et non sur l'infrastructure.

3. Créez un fichier texte nommé **taskdata3**, copiez-y le texte suivant, puis enregistrez le fichier.

	Les Sites web Azure fournissent un environnement évolutif, fiable et facile à utiliser pour l'hébergement d'applications web. Faites votre choix parmi toute une gamme d'infrastructures et de modèles pour créer un site web en quelques secondes. Utilisez l'outil ou le système d'exploitation de votre choix pour développer votre site avec .NET, PHP, Node.js ou Python. Faites votre choix parmi les diverses options de contrôle de code source, notamment TFS, GitHub et BitBucket, pour configurer une intégration continue et favoriser le développement en équipe. Développez les fonctionnalités de votre site au fil du temps grâce à des services gérés Azure supplémentaires tels que Azure Storage, Azure CDN et Azure SQL Database.

### Charger les fichiers dans le conteneur

1. Ouvrez le fichier Program.cs du projet GettingStarted, puis ajoutez-y la méthode ci-après qui charge les fichiers :

		static void CreateFiles()
		{
		  privateCloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
	  	CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
	  	CloudBlockBlob storageassembly =
			container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
		  taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);
		  dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);
		  storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);
		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. Enregistrez le fichier Program.cs.

## Étape 2. Ajouter un pool à votre compte

Un pool de nœuds de calcul est le premier jeu de ressources que vous devez créer quand vous souhaitez exécuter des tâches.

1.	Ajoutez les déclarations d’espace de noms ci-après en haut du fichier Program.cs du projet GettingStarted :

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Ajoutez à la section Main le code ci-après qui configure les informations d’identification pour effectuer des appels vers le service Azure Batch :

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("https://[account-name].[region].batch.azure.com", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Remplacez les valeurs suivantes :

	- Remplacez **[account-name]** par le nom du compte Batch que vous avez créé précédemment.
	- Remplacez **[region]** par la région dans laquelle se trouve votre compte. Pour découvrir les régions disponibles, voir l’article [Régions Azure](http://azure.microsoft.com/regions/).
	- Remplacez **[account-key]** par la clé primaire du compte Batch.

3.	Ajoutez à la classe Program la méthode ci-après qui crée le pool :

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		CreatePool(client);

5. Ajoutez à la classe Program la méthode ci-après qui répertorie les pools figurant dans le compte. Cette opération vous permet de vérifier que votre pool a été créé :

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		ListPools(client);

7. Enregistrez le fichier Program.cs.

## Étape 2 : ajouter un travail à un compte

Créez un travail permettant de gérer les tâches qui s’exécutent dans le pool. Toutes les tâches doivent être associés à un travail.

1. Ajoutez à la classe Program la méthode ci-après qui crée le travail :

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		CreateJob(client);

3. Ajoutez à la classe Program la méthode ci-après qui répertorie les travaux figurant dans le compte. Cette opération vous permet de vérifier que votre travail a été créé :

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		ListJobs(client);

5. Enregistrez le fichier Program.cs.

## Étape 3 : ajouter des tâches au travail

Une fois le travail créé, vous pouvez y ajouter des tâches. Chaque tâche s’exécute sur un nœud de calcul et traite un fichier texte. Pour ce didacticiel, vous ajoutez trois tâches au travail.

1. Ajoutez à la classe Program la méthode ci-après qui ajoute les trois tâches au travail :

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.azure.com/[]/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

	La valeur **[account-name]** doit être remplacée par le nom du compte de stockage que vous avez créé précédemment. Vérifiez que vous obtenez bien les quatre emplacements.

2. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

			AddTasks(client);

3. Ajoutez à la classe Program la méthode ci-après qui répertorie les tâches associées au travail :

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		ListTasks(client);

5. Enregistrez le fichier Program.cs.

## Étape 4 : supprimer les ressources

Étant donné que les ressources d’Azure vous sont facturées, il est recommandé de supprimer systématiquement les ressources dont vous n’avez plus besoin.

### Supprimer les tâches

1.	Ajoutez à la classe Program la méthode ci-après qui supprime les tâches :

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. Ajoutez à la section Main le code ci-après qui appelle la méthode que vous venez d’ajouter :

		DeleteTasks(client);

3. Enregistrez le fichier Program.cs.

### Supprimer le travail

1.	Ajoutez à la classe Program la méthode ci-après qui supprime le travail :

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. Ajoutez à la section Main le code ci-après qui exécute la méthode que vous venez d’ajouter :

		DeleteJob(client);

3. Enregistrez le fichier Program.cs.

### Suppression du pool

1. Ajoutez à la classe Program la méthode ci-après qui supprime le pool :

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Ajoutez à la section Main le code ci-après qui exécute la méthode que vous venez d’ajouter :

		DeletePool(client);

3. Enregistrez le fichier Program.cs.

## Étape 5 : exécution de l'application

1. Démarrez le projet GettingStarted. Vous devez alors voir apparaître la chaîne ci-après dans la fenêtre de console après la création du conteneur :

		Created the container. Press Enter to continue.

2. Appuyez sur Entrée pour créer et charger les fichiers. La fenêtre doit alors afficher une nouvelle ligne :

		Uploaded the files. Press Enter to continue.

3. Appuyez sur Entrée pour créer le pool :

		Created the pool. Press Enter to continue.

4. Appuyez sur Entrée pour visualiser la description du nouveau pool :

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. Appuyez sur Entrée pour créer le travail :

		Created the job. Press Enter to continue.

6. Appuyez sur Entrée pour visualiser la description du nouveau travail :

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. Appuyez sur Entrée pour ajouter les tâches au travail. Une fois que les tâches ont été ajoutées, elles s’exécutent automatiquement :

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. Appuyez sur Entrée pour visualiser la liste des tâches et leur état :

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. À ce stade, vous pouvez accéder au portail Azure pour examiner les ressources qui ont été créées. Pour supprimer les ressources, appuyez sur Entrée jusqu’à ce que le programme se termine.

## Étapes suivantes

1. À présent que vous connaissez les principes de base de l’exécution des tâches, vous pouvez découvrir la procédure de mise à l’échelle automatique des nœuds de calcul lorsque la demande concernant votre application évolue. Pour effectuer cette opération, voir l’article [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md).

2. Certaines applications génèrent de grandes quantités de données qui peuvent se révéler difficiles à traiter. L’un des moyens de contourner ce problème consiste à utiliser des [requêtes de liste efficaces](batch-efficient-list-queries.md).

<!---HONumber=August15_HO6-->