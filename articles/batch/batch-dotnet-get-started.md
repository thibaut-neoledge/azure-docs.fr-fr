<properties
	pageTitle="Didacticiel - Prise en main de la bibliothèque Azure Batch pour .NET"
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
	ms.date="05/04/2015"
	ms.author="yidingz"/>

#Prise en main de la bibliothèque Azure Batch pour .NET  

Cet article contient les deux didacticiels suivants pour vous aider à commencer à développer avec la bibliothèque .NET pour le service Azure Batch.

-	[Didacticiel 1 : bibliothèque Azure Batch pour .NET](#tutorial1)
-	[Didacticiel 2 : bibliothèque d'applications Azure Batch pour .NET](#tutorial2)  


Pour obtenir des informations générales et des scénarios pour Azure Batch, consultez [Présentation technique de Azure Batch](batch-technical-overview.md).

##<a name="tutorial1"></a>Didacticiel 1 : bibliothèque Azure Batch pour .NET

Ce didacticiel va vous montrer comment créer une application console qui configure le calcul distribué dans un pool de machines virtuelles à l'aide du service Azure Batch. Les tâches créées dans ce didacticiel évaluent le texte à partir de fichiers du stockage Azure et renvoient les mots les plus couramment utilisés. Les exemples ont été écrits en code C# et utilisent la bibliothèque Azure Batch pour .NET.


>[AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Vous pouvez créer un compte d’essai gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
>
>Vous devez utiliser NuGet pour obtenir l’assembly **Microsoft.Azure.Batch.dll**. Après avoir créé votre projet dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et sélectionnez **Gérer les packages NuGet**. Recherchez en ligne **Azure.Batch** puis cliquez sur Installer pour installer le package Azure Batch et les dépendances.
>
>Assurez-vous que votre version du gestionnaire de package NuGet est la version 2.8 ou ultérieure. Vous trouverez le numéro de version dans Visual Studio -> Aide -> Boîte de dialogue À propos de Microsoft Visual Studio. Si vous avez une version antérieure du gestionnaire de package NuGet, vous devez mettre à jour Visual Studio. Sinon, vous aurez peut-être des difficultés à télécharger la version appropriée des dépendances NuGet.
>
>Par ailleurs, vous pouvez vous reporter à l’[exemple Azure Batch Hello World](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c) sur MSDN pour obtenir un exemple similaire au code mentionné ici.



###Concepts
Le service Batch est utilisé pour la planification d'un calcul distribué et évolutif. Il permet d'exécuter des charges de travail à grande échelle, distribuées entre plusieurs machines virtuelles. Ces charges de travail prennent en charge efficacement le calcul intensif. Lorsque vous utilisez le service Batch, vous profitez des ressources suivantes :

-	**Compte** : une entité identifiée de manière unique au sein du service. Tout le traitement s'effectue via un compte Batch.
-	**Pool** : une collection de machines virtuelles de tâche sur lesquelles les applications de tâche sont exécutées.
-	**Machine virtuelle de tâche** : une machine qui est affectée à un pool et qui est utilisée par des tâches qui sont affectées aux travaux qui s'exécutent dans le pool.
-	**Utilisateur de la machine virtuelle de tâche** : un compte d'utilisateur sur une machine virtuelle de tâche.
-	**Élément de travail** : indique comment le calcul est effectué sur des machines virtuelles de tâche dans un pool.
-	**Travail** : l’instance exécutée d'un élément de travail et qui se compose d'une collection de tâches.
-	**Tâches** : une application qui est associée à un travail et s'exécute sur une machine virtuelle de tâche.
-	**Fichier** : contient les informations traitées par une tâche.  

Commençons par l'utilisation la plus simple.

###Création d'un compte Azure Batch
Pour créer un compte Batch, vous pouvez utiliser le portail de gestion. Une clé vous est fournie une fois le compte créé. Pour plus d'informations, consultez [Présentation technique d’Azure Batch](batch-technical-overview.md).

###Procédure d’ajout de pool à un compte
Un pool de machines virtuelles de tâche est le premier jeu de ressources que vous devez créer lorsque vous souhaitez exécuter des tâches.

1.	Ouvrez Microsoft Visual Studio 2013, dans le menu **Fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet**.

2.	À partir de **Windows**, sous **Visual C#**, cliquez sur **Application console**, nommez le projet **GettingStarted**, nommez la solution **AzureBatch**, puis cliquez sur **OK**.

3.	Ajoutez les déclarations d'espace de noms suivantes en haut du fichier Program.cs :

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	Veillez à référencer l'assembly Microsoft.Azure.Batch.dll.

4.	Ajoutez les variables suivantes à la classe Program :

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	Remplacez les valeurs suivantes : - **[name-of-pool]** : le nom que vous souhaitez utiliser pour le pool. - **[name-of-batch-account]** : le nom du compte Batch - **[key-of-batch-account]** : la clé qui vous a été fournie pour le compte Batch.
5.	Ajoutez le code suivant à la section Main pour définir les informations d'identification à utiliser :

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	Ajoutez le code suivant dans la section Main pour créer le client utilisé pour effectuer des opérations :

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	Ajoutez le code suivant à la section Main pour créer le pool s'il n'existe pas :

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName,
		         osFamily: "3",
		         vmSize: "small",
		         targetDedicated: NumOfMachines);
		       newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	Enregistrez et exécutez le programme. L'état est **Actif** pour un pool correctement ajouté.  

###Procédure pour répertorier les pools dans un compte
Si vous ne connaissez pas le nom d'un pool existant, vous pouvez obtenir une liste dans un compte.

1.	Ajoutez le code suivant à la section Main pour définir les informations d'identification à utiliser :  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	Ajoutez le code suivant dans la section Main pour créer le client utilisé pour effectuer des opérations :

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	Mettez à jour la procédure Main avec le code suivant qui enregistre les noms et les états de tous les pools dans le compte et crée le pool s'il n'existe pas :

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);

		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();

		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  

		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	Enregistrez et exécutez le programme. La sortie suivante s'affiche.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###Procédure pour répertorier les éléments de travail dans un compte
Si vous ne connaissez pas le nom d'un élément de travail existant, vous pouvez obtenir une liste dans un compte.

1.	Ajoutez le code suivant à la fin de la section Main pour enregistrer les noms et les états de tous les éléments de travail dans le compte :

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }
		}
		Console.WriteLine("Press <Enter> to continue.");
		Console.ReadLine();
7.	Enregistrez et exécutez le programme. Vous ne verrez probablement rien, car nous n'avons pas soumis d'élément de travail. Nous reviendrons sur l'ajout d'éléments de travail dans la section suivante.  

##Procédure d’ajout d’un élément de travail à un compte
Vous devez créer un élément de travail pour définir la façon dont les tâches sont exécutées dans le pool.

1.	Ajoutez les variables suivantes à la classe Program :

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	Quand un élément de travail est créé, un travail est également créé. Vous pouvez attribuer un nom à l'élément de travail, mais le travail se voit toujours attribuer le nom de **job-0000000001**. Ajoutez le code suivant à la section Main (avant le code d'un élément de travail de liste) pour ajouter l'élément de travail :

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	Enregistrez et exécutez le programme. L'état est **Actif** pour un élément de travail correctement ajouté. La sortie suivante s'affiche.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###Procédure d’ajout de tâches à un travail
Un élément de travail sans tâche ne fera rien. Après avoir créé l'élément de travail et le travail, vous pouvez y ajouter des tâches. Ajoutons une tâche simple au travail.

1.	Ajoutez les variables suivantes à la classe Program :

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	Ajoutez le code suivant à la section Main pour ajouter des tâches à un travail, attendre leur exécution et générer un rapport sur les résultats :

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }

		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	Enregistrez et exécutez le programme. Le résultat doit avoir l'aspect suivant :

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1

		Task taskdata2 says:
		I am taskdata2

		Task taskdata3 says:
		I am taskdata3

###Création d'un programme de traitement de tâche
Maintenant que nous pouvons exécuter « Hello world » sur une machine virtuelle, passons aux choses sérieuses. Nous allons créer un programme de traitement de tâche dans cette section et le télécharger sur la machine virtuelle de tâche qui exécute des tâches.

1.	Dans l'Explorateur de solutions, créez un nouveau projet d'application console nommé **ProcessTaskData** dans la solution **AzureBatch**.

2.	Ajoutez les déclarations d'espace de noms suivantes en haut du fichier Program.cs :

		using System.Net;

3.	Ajoutez le code suivant à la section Main pour traiter les données :

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);

		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);

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

4.	Enregistrez et générez le projet.

###Préparation des ressources pour l'exécution de tâches

Vous utiliserez le flux de travail de base suivant lorsque vous allez créer un scénario de calcul distribué avec le service Batch :

1.	Téléchargez les fichiers que vous souhaitez utiliser dans votre scénario de calcul distribué dans un compte de stockage Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Les fichiers sont chargés sur une machine virtuelle de tâche quand la tâche s'exécute.
2.	Téléchargez les fichiers binaires dépendants dans le compte de stockage, notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans la machine virtuelle de tâche.
3.	Créez un pool de machines virtuelles de tâche. Vous pouvez attribuer la taille de la machine virtuelle de tâche à utiliser lors de la création du pool. Quand une tâche s'exécute, elle reçoit une machine virtuelle à partir de ce pool.
4.	Créez un élément de travail. Un élément de travail vous permet de gérer un travail constitué de tâches. Un travail est créé automatiquement lorsque vous créez un élément de travail.
5.	Ajoutez des tâches au travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.
6.	Analysez les résultats de la sortie.  

Nous avons montré les étapes 3 à 6. Voyons maintenant comment préparer Azure Storage pour l'exécution de la tâche.

####Obtention du compte de stockage
Vous devez avoir un compte de stockage pour suivre le reste de ce didacticiel. Si vous ne savez pas comment procéder, consultez [Création d’un compte Azure Storage](#tutorial1_storage).

####Téléchargement de données

1. Créez un conteneur appelé « gettingstarted » dans votre compte Azure Storage. Pour cela, utilisez le portail Azure. Veillez à définir le champ « ACCÈS » sur « Conteneur Public ».

2. Téléchargez ProcessTaskData.exe dans le conteneur.

3. Créez trois fichiers texte (taskdata1.txt, taskdata2.txt, taskdata3.txt), chacun contenant un des paragraphes ci-dessous, puis téléchargez-les dans le conteneur :

		You can use Azure Virtual Machines to provision on-demand, scalable compute infrastructure when you need flexible resources for your business needs. From the gallery, you can create virtual machines that run Windows, Linux, and enterprise applications such as SharePoint and SQL Server. Or, you can capture and use your own images to create customized virtual machines.

		Quickly deploy and manage powerful applications and services with Azure Cloud Services. Simply upload your application and Azure handles the deployment details - from provisioning and load balancing to health monitoring for continuous availability. Your application is backed by an industry leading 99.95% monthly SLA. You just focus on the application and not the infrastructure.

		Azure Web Sites provide a scalable, reliable, and easy-to-use environment for hosting web applications. Select from a range of frameworks and templates to create a web site in seconds. Use any tool or OS to develop your site with .NET, PHP, Node.js or Python. Choose from a variety of source control options including TFS, GitHub, and BitBucket to set up continuous integration and develop as a team. Expand your site functionality over time by leveraging additional Azure managed services like storage, CDN, and SQL Database.


>[AZURE.NOTE]Dans un environnement de production, il est recommandé d'utiliser une signature d'accès partagé.


>[AZURE.NOTE]L'équipe Azure Storage a rédigé un [billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) qui répertorie les explorateurs Azure Storage qui permettent de télécharger des fichiers.



###Mise à jour du code d'envoi de tâche

1.	Ajoutez les variables suivantes à la classe Program :

		private const string BlobPath = "[storage-path]";
	Remplacez les valeurs suivantes : - **[storage-path]** : chemin d'accès de l'objet blob dans le stockage. Par exemple : http://yiding.blob.core.windows.net/gettingstarted/

2. Mettez à jour le code d'envoi de tâche comme suit.

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit();

3. Enregistrez et exécutez le programme. Ce qui suit doit s'afficher :

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3

		Task taskdata2 says:
		and 5
		application 3
		the 3

		Task taskdata3 says:
		a 5
		and 5
		to 3

###Procédure de suppression du pool et de l'élément de travail
Après le traitement de votre élément de travail, vous pouvez libérer des ressources en supprimant le pool et l'élément de travail.

####Suppression du pool
1.	Ajoutez le code suivant à la fin de la section Main pour supprimer le pool :

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	Enregistrez et exécutez le programme.


####Suppression de l'élément de travail
1.	Ajoutez le code suivant à la section Main pour supprimer l'élément de travail :

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	Enregistrez et exécutez le programme.

###<a name="tutorial1_storage"></a>ANNEXE : création d'un compte Azure Storage
Avant d'exécuter le code de ce didacticiel, vous devez avoir accès à un compte de stockage dans Azure.

1.	Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).
2.	En bas du volet de navigation, cliquez sur **NOUVEAU**. ![][1]
3.	Cliquez sur **SERVICES DE DONNÉES**, puis sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**. ![][2]

4.	Dans **URL**, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement.
5.	Choisissez un **EMPLACEMNENT/GROUPE D’AFFINITÉS** dans lequel situer le stockage.
6.	Vous pouvez éventuellement activer les géo-réplications.
7.	Cliquez sur **CREATE STORAGE ACCOUNT**.  

Pour plus d'informations sur Azure Storage, consultez [Utilisation du service de stockage d'objets blob Azure dans .NET](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).


##<a name="tutorial2"></a>Didacticiel 2 : bibliothèque d'applications Azure Batch pour .NET
Ce didacticiel vous montre comment exécuter des charges de travail de calcul parallèles sur Azure Batch à l'aide du service Batch Apps.

Batch Apps est une fonctionnalité d'Azure Batch qui offre un moyen (centré sur les applications) de gérer et d'exécuter des charges de travail Batch. En utilisant le Kit de développement logiciel (SDK) Batch Apps, vous pouvez créer des packages permettant d'activer Batch dans une application, les déployer dans votre propre compte ou les rendre disponibles aux autres utilisateurs de Batch. Batch fournit également la gestion des données, la surveillance des travaux, les diagnostics et la journalisation intégrés, et la prise en charge des dépendances entre les tâches. En outre, il inclut un Portail de gestion où vous pouvez gérer les travaux, afficher les journaux et télécharger des sorties sans avoir à écrire votre propre client.

Dans un scénario Batch Apps, vous écrivez du code utilisant le Kit de développement logiciel (SDK) Batch Apps Cloud pour partitionner des travaux en tâches parallèles, décrire toutes les dépendances entre ces tâches et spécifier l'exécution de chaque tâche. Ce code est déployé sur le compte Batch. Les clients peuvent ensuite exécuter les travaux simplement en spécifiant le type de travail et les fichiers d'entrée à une API REST.

>[AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Vous pouvez créer un compte d’essai gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/). Vous pouvez utiliser NuGet pour obtenir les assemblys <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch Apps Cloud</a>et <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch Apps Client</a>. Après avoir créé votre projet dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et sélectionnez **Gérer les package NuGet**. Vous pouvez également télécharger l'extension Visual Studio pour Batch Apps qui inclut un modèle de projet permettant d’activer les applications dans le cloud et de déployer une application <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">ici</a> ou en recherchant **Batch Apps** dans Visual Studio via l'option de menu Extensions et mises à jour. Vous trouverez également des <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">exemples de bout en bout sur MSDN.</a>
>

###Principes de base d'Azure Batch Apps
Batch est conçu pour fonctionner avec les applications de calcul intensif existantes. Il tire parti de votre code d'application existant et l'exécute dans un environnement virtualisé, dynamique et à usage général. Pour activer une application pour qu'elle fonctionne avec Batch Apps, plusieurs opérations sont nécessaires :

1.	Préparez un fichier .zip de vos fichiers exécutables d'application existants (ceux que vous exécuteriez dans une batterie de serveurs ou un cluster classique) et les fichiers de prise en charge nécessaires. Ce fichier .zip est ensuite téléchargé vers votre compte Batch à l'aide du Portail de gestion ou de l'API REST.
2.	Créez un fichier .zip des « assemblys cloud » qui distribuent vos charges de travail à l'application, puis téléchargez-le via le Portail de gestion ou l'API REST. Un assembly cloud contient deux composants qui reposent sur le Kit de développement logiciel (SDK) Cloud :
	1.	Outil de fractionnement du travail, qui décompose le travail en tâches qui peuvent être traitées indépendamment. Par exemple, dans un scénario d'animation, l'outil de fractionnement du travail prend une vidéo et la décompose en images individuelles.
	2.	Processeur de tâches, qui appelle l'application exécutable pour une tâche donnée. Par exemple, dans un scénario d'animation, le processeur de tâches appelle un programme de rendu pour restituer l'image unique spécifiée par la tâche en cours d'exécution.
3.	Fournissez un moyen d'envoyer des travaux à l'application activée dans Azure. Cela peut être un plug-in dans l'interface utilisateur de votre application ou un portail web, voire un service sans assistance dans le cadre de votre pipeline d'exécution. Pour obtenir des <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">exemples</a>, consultez la page Exemples sur MSDN.



###Concepts fondamentaux Batch Apps
Le modèle de programmation et d'utilisation de Batch Apps repose sur les concepts clés suivants :

####Travaux
Un **travail** est un élément de travail soumis par l'utilisateur. Lorsqu'un travail est envoyé, l'utilisateur spécifie le type de travail, les paramètres associés et les données requises. L'implémentation activée peut se charger de ces détails au nom de l'utilisateur. Dans certains cas, l'utilisateur peut fournir ces informations explicitement via le client. Un travail est associé à des résultats qui sont renvoyés. Chaque travail possède une sortie principale et éventuellement une sortie préliminaire. Les travaux peuvent également retourner des sorties supplémentaires, le cas échéant.

####Tâches
Un **tâche** est un élément de travail à effectuer dans le cadre d'un travail. Lorsqu'un utilisateur envoie un travail, il est divisé en tâches plus petites. Le service traite ensuite ces tâches individuelles, puis assemble les résultats de la tâche dans une sortie de travail globale. La nature des tâches dépend du type de travail. L'outil de fractionnement du travail définit la manière dont un travail est divisé en tâches, en fonction de ce qu'il sait sur ce que l'application est amenée à traiter. Les sorties de tâche peuvent également être téléchargées séparément et peuvent être utiles dans certains cas, par exemple, quand un utilisateur souhaite télécharger des tâches individuelles à partir d'un travail d'animation.

####Tâches de fusion
Un **tâche de fusion** est un type spécial de tâche qui assemble les résultats des tâches individuelles dans le résultat final du travail. Pour un travail de rendu de film, la tâche de fusion peut assembler les images restituées dans un film ou les compresser en un seul fichier. Chaque travail possède une tâche de fusion, même si en réalité, aucune « fusion » n'est nécessaire.

####Fichiers
Un **fichier** est un élément de données utilisé comme entrée d’un travail. Un travail peut avoir autant de fichiers d'entrée associés que nécessaire (zéro, un ou plusieurs). Le même fichier peut être utilisé dans plusieurs travaux, par exemple, pour un travail de rendu d’un film, les fichiers peuvent contenir des textures, des modèles, etc. Pour un travail d'analyse de données, les fichiers peuvent être un jeu d’observations ou de mesures.

###Activation de l'application cloud
Votre application doit contenir une propriété ou un champ statique qui contient tous les détails de votre application (spécification du nom de l'application et du ou des types de travail gérés par l'application). Ceci est fourni durant l'utilisation du modèle dans le Kit de développement logiciel (SDK) qui peut être téléchargé via la galerie Visual Studio.

Voici un exemple de déclaration d'application cloud pour une charge de travail parallèle :

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implémentation de l'outil de fractionnement du travail et du processeur de tâches
Pour les charges de travail parallèles, vous devez implémenter un outil de fractionnement du travail et un processeur de tâches.

####Implémentation de JobSplitter.SplitIntoTasks
Votre implémentation SplitIntoTasks doit retourner une séquence de tâches. Chaque tâche représente un élément de travail distinct qui sera mis en file d'attente à des fins de traitement par un nœud de calcul. Chaque tâche doit être autonome et configurée avec toutes les informations dont le processeur de tâches aura besoin.

Les tâches spécifiées par l'outil de fractionnement du travail sont représentées en tant qu'objets TaskSpecifier. TaskSpecifier a un nombre de propriétés que vous pouvez définir avant de retourner la tâche.


-	TaskIndex est ignoré, mais disponible pour les processeurs de tâches. Cela permet de transmettre un index au processeur de tâches. Si vous n'avez pas besoin de transmettre un index, il n'est pas nécessaire de définir cette propriété.
-	Parameters correspond par défaut à une collection vide. Vous pouvez l'ajouter ou la remplacer par une nouvelle collection. Vous pouvez copier les entrées de la collection de paramètres de travail à l'aide de la méthode WithJobParameters ou WithAllJobParameters.  


RequiredFiles correspond par défaut à une collection vide. Vous pouvez l'ajouter ou la remplacer par une nouvelle collection. Vous pouvez copier les entrées de la collection de fichiers de travail à l'aide de la méthode RequiringJobFiles ou RequiringAllJobFiles.

Vous pouvez spécifier une tâche qui dépend d'une autre tâche. Pour ce faire, définissez la propriété TaskSpecifier.DependsOn, en transmettant l'ID de la tâche dont elle dépend :

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

La tâche ne s'exécutera pas tant que la sortie de la tâche dépendante ne sera pas disponible.

Vous pouvez également indiquer que l'ensemble d'un groupe de tâches ne démarre pas le traitement tant qu'un autre groupe n'est pas complètement terminé. Dans ce cas, vous pouvez définir la propriété TaskSpecifier.Stage. Les tâches avec une valeur d'étape donnée ne commencent pas le traitement tant que toutes les tâches ayant des valeurs d'étape inférieures ne sont pas terminées ; par exemple, des tâches avec l'étape 3 ne commencent pas le traitement tant que toutes les tâches des étapes 0, 1 ou 2 ne sont pas terminées. Les étapes doivent commencer à 0, la séquence d'étapes ne doit pas comporter de blancs. En outre, SplitIntoTasks doit retourner les tâches dans l'ordre des étapes : par exemple, retourner une tâche à l'étape 0 après une tâche à l'étape 1 constitue une erreur.

Chaque travail parallèle se termine par une tâche particulière appelée tâche de fusion. Le travail de la tâche de fusion consiste à assembler les résultats des tâches de traitement parallèle dans un résultat final. Batch Apps crée automatiquement la tâche de fusion.

Dans de rares cas, vous pouvez contrôler explicitement la tâche de fusion, par exemple, pour personnaliser ses paramètres. Dans ce cas, vous pouvez spécifier la tâche de fusion en renvoyant un TaskSpecifier avec sa propriété IsMerge définie sur true. Cela remplacera la tâche de fusion automatique. Si vous créez une tâche de fusion explicite :

-	Vous pouvez créer une seule tâche de fusion explicite
-	Elle doit être la dernière tâche de la séquence
-	Elle doit avoir IsMerge défini sur true, et toutes les autres tâches doivent avoir IsMerge défini sur false  


Toutefois, n'oubliez pas que normalement vous n'avez pas besoin de créer la tâche de fusion explicitement.

Le code suivant illustre une implémentation simple de SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implémentation de ParallelTaskProcessor.RunExternalTaskProcess

RunExternalTaskProcess est appelé pour chaque tâche de fusion non renvoyée par l'outil de fractionnement du travail. Il doit appeler votre application avec les arguments appropriés, et retourner une collection de sorties qui doivent être conservées pour être utilisées plus tard.

Le fragment suivant montre comment appeler un programme nommé application.exe. Notez que vous pouvez utiliser la méthode d'assistance ExecutablePath pour créer des chemins d'accès de fichiers absolus.

La classe ExternalProcess du Kit de développement logiciel (SDK) Cloud fournit une logique d'assistance pour exécuter vos fichiers exécutables d'application. ExternalProcess peut prendre en charge l'annulation, la conversion des codes de sortie en exceptions, la capture de sorties et d'erreurs standard et la configuration des variables d'environnement. Vous pouvez également utiliser la classe Process .NET directement pour exécuter vos programmes.

Votre méthode RunExternalTaskProcess renvoie un TaskProcessResult, qui comprend une liste de fichiers de sortie. Celle-ci doit inclure au moins tous les fichiers requis pour la fusion ; vous pouvez également renvoyer des fichiers journaux, des fichiers d'aperçu et des fichiers intermédiaires (par exemple à des fins de diagnostic si la tâche a échoué). Notez que la méthode renvoie les chemins d'accès du fichier, et non son contenu.

Chaque fichier doit être identifié par le type de sortie qu'il contient : sortie (autrement dit, partie de la sortie de travail finale), version préliminaire, journal ou résultat intermédiaire. Ces valeurs proviennent de l'énumération TaskOutputFileKind. Le fragment suivant renvoie une sortie de tâche unique et aucun aperçu ni journal. La méthode TaskProcessResult.FromExternalProcessResult simplifie le scénario courant de capture du code de sortie, de la sortie de processeur et des fichiers de sortie à partir d'un programme de ligne de commande :

Le code suivant illustre une implémentation simple de ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implémentation de ParallelTaskProcessor.RunExternalMergeProcess

Ce processus est appelé pour la tâche de fusion. Il doit appeler l'application pour combiner les sorties des tâches précédentes, de la façon appropriée pour votre application, et renvoyer la sortie combinée.

L'implémentation de RunExternalMergeProcess est très similaire à celle de RunExternalTaskProcess, à ceci près que :

-	RunExternalMergeProcess consomme les sorties des tâches précédentes, plutôt que les fichiers d'entrée utilisateur. Vous devez donc définir les noms des fichiers que vous souhaitez traiter en fonction de l'ID de tâche, plutôt qu'à partir de la collection Task.RequiredFiles.
-	RunExternalMergeProcess renvoie un fichier JobOutput et éventuellement un fichier JobPreview.


Le code suivant illustre une implémentation simple de ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Envoi de travaux à Batch Apps
Un travail décrit une charge de travail à exécuter et doit inclure toutes les informations sur la charge de travail à l'exception du contenu du fichier. Par exemple, le travail contient des paramètres de configuration qui vont du client à l'outil de fractionnement du travail, puis aux tâches. Les exemples fournis sur MSDN montrent comment envoyer des tâches et fournir plusieurs clients, y compris un portail web et un client de ligne de commande.


<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg

<!---HONumber=July15_HO3-->