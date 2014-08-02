<properties linkid="develop-media-services-how-to-guides-check-job-progress" urlDisplayName="Check Job Progress" pageTitle="How to Check Job Progress in Media Services - Azure" metaKeywords="" description="Learn how to use event handler code to track job progress and send status updates. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Check Job Progress" authors="migree" solutions="" manager="" editor="" />

Suivi de la progression des tâches
==================================

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Encodage d'un élément multimédia](http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409).

Lorsque vous exécutez des tâches, vous avez généralement besoin de faire appel à une méthode de suivi de la progression de la tâche. L'exemple de code suivant définit un gestionnaire d'événements StateChanged. Ce dernier suit la progression de la tâche et fournit l'état mis à jour, selon l'état. Le code définit également la méthode LogJobStop. Cette méthode d'assistance journalise les détails de l'erreur.

``` {}
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
            // Effectuez une conversion de type de l'expéditeur en tant que tâche.
            IJob job = (IJob)sender;
            // Affichez ou notez les détails de l'erreur si nécessaire.
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
    // Consignez les erreurs de la tâche, le cas échéant.  
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
    // Consignez la sortie dans un fichier local et sur la console. Le modèle 
    // de fichier d'erreurs de sortie est :  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
```

Étapes suivantes
================

Vous savez maintenant créer une tâche et suivre sa progression. L'étape suivante consiste à protéger les éléments multimédias. Pour plus d'informations, consultez la page [Protection d'éléments multimédias avec Azure Media Services](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

