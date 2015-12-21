
<properties 
	pageTitle="Gestion des éléments multimédias et des entités connexes avec le Kit de développement logiciel (SDK) Media Services .NET" 
	description="Apprenez à gérer des éléments multimédias et leurs entités associées avec le Kit de développement logiciel (SDK) pour .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015"
	ms.author="juliako"/>


#Gestion des éléments multimédias et des entités connexes avec le Kit de développement logiciel (SDK) Media Services .NET


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [REST](media-services-rest-manage-entities.md)


Cette rubrique explique comment effectuer les tâches de gestion Media Services suivantes :

- Obtenir une référence pointant vers un élément multimédia 
- Obtenir une référence pointant vers un travail 
- Répertorier tous les éléments multimédias 
- Répertorier les travaux et les éléments multimédias 
- Répertorier toutes les stratégies d’accès 
- Répertorier tous les localisateurs 
- Supprimer un élément multimédia 
- Supprimer un travail 
- Supprimer une stratégie d’accès 

##Conditions préalables 

Consultez [Configuration de votre environnement](media-services-set-up-computer.md)

##Obtenir une référence pointant vers un élément multimédia

L’obtention d’une référence pointant vers un élément multimédia existant dans Media Services est une tâche fréquente. L’exemple de code suivant montre comment vous pouvez obtenir une référence pointant vers un élément multimédia à partir de la collection Assets sur l’objet de contexte du serveur, en fonction d’un ID d’élément multimédia. L’exemple de code suivant utilise une requête Linq pour obtenir une référence pointant vers un objet IAsset existant.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##Obtenir une référence pointant vers un travail

Lorsque vous utilisez des tâches de traitement dans le code de Media Services, vous devez souvent obtenir une référence pointant vers un travail existant basé sur un ID. L’exemple de code suivant montre comment obtenir une référence pointant vers un objet IJob à partir de la collection Jobs. Attention : il est possible que vous deviez obtenir une référence pointant vers un travail lors du démarrage d’un travail d’encodage à long terme et vérifier l’état du travail sur un thread. Dans ce cas, lorsque la méthode est retournée à partir d’un thread, vous devez récupérer une référence actualisée pointant vers le travail.

	static IJob GetJob(string jobId)
	{
	    // Use a Linq select query to get an updated 
	    // reference by Id. 
	    var jobInstance =
	        from j in _context.Jobs
	        where j.Id == jobId
	        select j;
	    // Return the job reference as an Ijob. 
	    IJob job = jobInstance.FirstOrDefault();
	
	    return job;
	}

##Répertorier tous les éléments multimédias

Lorsque le nombre d’éléments multimédias de votre stockage augmente, il est utile de les répertorier. L’exemple de code suivant montre comment parcourir la collection Assets sur l’objet de contexte du serveur. Pour chaque élément multimédia, l’exemple de code écrit également certaines de ses valeurs de propriétés vers la console. Par exemple, chaque élément multimédia peut contenir plusieurs fichiers multimédias. L’exemple de code écrit tous les fichiers associés à chaque élément multimédia.



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Répertorier les travaux et les éléments multimédias

Une tâche associée importante consiste à répertorier les éléments multimédias avec leur travail associé dans Media Services. L’exemple de code suivant montre comment répertorier chaque objet IJob, puis afficher les propriétés de chaque travail, toutes les tâches associées, ainsi que tous les éléments multimédias d’entrée et de sortie. Cet exemple de code peut être utilise pour bien d’autres tâches. Par exemple, si vous souhaitez répertorier les éléments multimédias de sortie à partir d’un ou plusieurs travaux d’encodage que vous avez exécutés précédemment, ce code montre comment accéder aux éléments multimédias de sortie. Lorsque vous avez une référence pointant vers un élément multimédia de sortie, vous pouvez alors livrer le contenu à d’autres utilisateurs ou applications en le téléchargeant ou en fournissant des URL.

Pour plus d’informations sur les options pour la livraison des éléments multimédias, consultez la page [Livraison d’éléments multimédias à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET](media-services-deliver-streaming-content.md).

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Répertorier toutes les stratégies d’accès

Dans Media Services, vous pouvez définir une stratégie d’accès sur un élément multimédia ou ses fichiers. Une stratégie d’accès définit les autorisations pour un fichier ou un élément multimédia (le type d’accès et la durée). Dans votre code Media Services, vous définissez généralement une stratégie d’accès en créant un objet IAccessPolicy et en l’associant à un élément multimédia existant. Ensuite, vous créez un objet ILocator, qui vous permet de livrer un accès direct aux éléments multimédias dans les Media Services. Le projet Visual Studio qui accompagne cette documentation contient plusieurs exemples de code qui montrent comment créer et attribuer des localisateurs et les stratégies d’accès aux éléments multimédias.

L’exemple de code suivant montre comment répertorier toutes les stratégies d’accès sur le serveur et quel type d’autorisations leur est associé. Une autre méthode d’affichage des stratégies d’accès consiste à répertorier tous les objets ILocator sur le serveur, puis répertorier les stratégies d’accès associées à chaque localisateur à l’aide de leur propriété AccessPolicy.

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##Répertorier tous les localisateurs

Un localisateur est une URL qui fournit un chemin d’accès direct pour accéder à un élément multimédia, ainsi que les autorisations pour accéder à l’élément multimédia, comme défini par la stratégie d’accès associée au localisateur. Chaque élément multimédia peut avoir une collection d’objets ILocator associée à sa propriété Locators. Le contexte de serveur possède également une collection Locators contenant tous les localisateurs.

L’exemple de code suivant répertorie tous les localisateurs sur le serveur. Il affiche l’ID des éléments multimédias et les stratégies d’accès associés à chaque localisateur. Il affiche également le type d’autorisation, la date d’expiration et le chemin d’accès complet à l’élément multimédia.

Notez qu’un chemin d’accès de localisateur vers un élément multimédia est simplement une URL de base pointant vers l’élément multimédia. Pour créer un chemin d’accès direct vers les fichiers individuels auxquels peut accéder un utilisateur ou une application, votre code doit ajouter le chemin d’accès de fichier spécifique au chemin d’accès du localisateur. Pour plus d’informations sur la procédure à suivre, consultez [Livraison d’éléments multimédias à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET](media-services-deliver-streaming-content.md).

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}


##Supprimer un élément multimédia

L’exemple suivant montre la suppression d’un élément multimédia.

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##Supprimer un travail

Pour supprimer un travail, vous devez vérifier l’état associé, comme indiqué dans la propriété State. Les travaux terminés ou annulés peuvent être supprimés, tandis que pour les travaux dans d’autres états, par exemple en file d’attente, planifiés ou en cours de traitement, il faut d’abord procéder à une annulation, puis supprimer le travail. L’exemple de code suivant montre une méthode de suppression de travail qui vérifie l’état des travaux, puis supprime ceux terminés ou annulés. Ce code est basé sur la section précédente de cette rubrique pour obtenir une référence pointant vers un travail : Obtenir une référence pointant vers un travail.

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##Supprimer une stratégie d’accès

L’exemple de code suivant montre comment obtenir une référence pointant vers une stratégie d’accès en fonction d’un ID de stratégie, puis comment supprimer cette stratégie.

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->