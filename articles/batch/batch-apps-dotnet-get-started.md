<properties
	pageTitle="Didacticiel - Prise en main de la bibliothèque Azure Batch Apps pour .NET"
	description="Découvrez les concepts de base d’Azure Batch Apps et la procédure de développement dans le cas d’un scénario simple"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# Prise en main de la bibliothèque Azure Batch Apps pour .NET
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

<!---HONumber=August15_HO6-->