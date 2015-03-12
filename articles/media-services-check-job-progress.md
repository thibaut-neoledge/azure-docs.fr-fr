<properties 
	pageTitle="Vérification de la progression des tâches à l'aide de .NET" 
	description="Apprenez à utiliser le code du gestionnaire d'événements pour suivre la progression des tâches et envoyer des mises à jour de l'état. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>

#Procédure : Progression des tâches

Cet article fait partie de la série [workflow à la demande de vidéo Media Services](../media-services-video-on-demand-workflow). 

Lorsque vous exécutez des travaux, vous avez généralement besoin de faire appel à une méthode de suivi de la progression du travail. L'exemple de code suivant définit un gestionnaire d'événements StateChanged. Ce dernier suit la progression du travail et fournit l'état mis à jour, selon l'état. Le code définit également la méthode LogJobStop. Cette méthode d'assistance journalise les détails de l'erreur.

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
	    Console.WriteLine("Job state changed event:");
	    Console.WriteLine("  Previous state: " + e.PreviousState);
	    Console.WriteLine("  Current state: " + e.CurrentState);
	
	    switch (e.CurrentState)
	    {
	        case JobState.Finished:
	            Console.WriteLine();
	            Console.WriteLine("********************");
	            Console.WriteLine("Job is finished.");
	            Console.WriteLine("Please wait while local tasks or downloads complete...");
	            Console.WriteLine("********************");
	            Console.WriteLine();
	            Console.WriteLine();
	            break;
	        case JobState.Canceling:
	        case JobState.Queued:
	        case JobState.Scheduled:
	        case JobState.Processing:
	            Console.WriteLine("Please wait...\n");
	            break;
	        case JobState.Canceled:
	        case JobState.Error:
	            // Cast sender as a job.
	            IJob job = (IJob)sender;
	            // Display or log error details as needed.
	            LogJobStop(job.Id);
	            break;
	        default:
	            break;
	    }
	}
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
	    // Log job errors if they exist.  
	    if (job.State == JobState.Error)
	    {
	        builder.Append("Error Details: \n");
	        foreach (ITask task in job.Tasks)
	        {
	            foreach (ErrorDetail detail in task.ErrorDetails)
	            {
	                builder.AppendLine("  Task Id: " + task.Id);
	                builder.AppendLine("    Error Code: " + detail.Code);
	                builder.AppendLine("    Error Message: " + detail.Message + "\n");
	            }
	        }
	    }
	    builder.AppendLine("***************************\n");
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}

<!--HONumber=45--> 
