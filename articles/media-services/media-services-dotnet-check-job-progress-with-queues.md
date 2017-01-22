---
title: Utiliser Azure Queue Storage pour surveiller les notifications de travaux Media Services avec .NET | Microsoft Docs
description: "Découvrez comment utiliser Azure Queue Storage pour surveiller les notifications de travaux Media Services. L’exemple de code est écrit en C# et utilise le Kit de développement logiciel (SDK) Media Services pour .NET."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 876b6a81c5fba7cd9567f913860dd5bdc2391c15


---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Utiliser Azure Queue Storage pour surveiller les notifications de travaux Media Services avec .NET
Lorsque vous exécutez des travaux, vous avez généralement besoin de faire appel à une méthode de suivi de la progression du travail. Vous pouvez vérifier la progression en utilisant Azure Queue Storage pour contrôler les notifications de travaux Media Services (comme le décrit cette rubrique) ou en définissant un gestionnaire d’événements StateChanged (comme le décrit [cette](media-services-check-job-progress.md) rubrique).  

## <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications"></a>Utiliser le stockage de files d’attente Azure pour surveiller les notifications de tâches Media Services
Microsoft Azure Media Services peut assurer la remise de messages de notification au [stockage de files d'attente Azure](../storage/storage-dotnet-how-to-use-queues.md) pendant le traitement de tâches multimédias. Cette rubrique explique comment obtenir ces messages de notification à partir de Queue Storage.

Les messages transmis au stockage de files d’attente sont accessibles n’importe où dans le monde. L’architecture de messagerie des files d’attente Azure est hautement évolutive. Le stockage avec interrogation des files d’attente est préférable aux autres méthodes.

Un scénario courant pour écouter les notifications Media Services se présente si vous développez un système de gestion de contenu qui doit exécuter une tâche supplémentaire une fois une tâche d’encodage terminée (par exemple, déclencher l’étape suivante d’un flux de travail ou publier du contenu).

### <a name="considerations"></a>Considérations
Considérez les éléments suivants lors du développement d’applications Media Services qui utilisent les files d’attente de stockage Azure.

* Le service de files d’attente ne garantit pas une remise dans l’ordre d’arrivée (FIFO). Pour plus d'informations, consultez [Files d'attente Azure et files d'attente Azure Service Bus - comparaison et différences](https://msdn.microsoft.com/library/azure/hh767287.aspx).
* Les files d’attente de stockage Azure ne constituent pas un service de type Push ; vous devez interroger les files d’attente.
* Le nombre de files d’attente est illimité. Pour plus d'informations, consultez [API REST du service de file d'attente](https://docs.microsoft.com/rest/api/storageservices/fileservices/Queue-Service-REST-API).
* Les files d'attente de stockage Azure présentent certaines limitations et spécificités décrites dans l'article suivant : [Files d'attente Azure et files d'attente Azure Service Bus - comparaison et différences](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)

### <a name="code-example"></a>Exemple de code
L’exemple de code de cette section permet d’effectuer les opérations suivantes :

1. Définir la classe **EncodingJobMessage** qui assure le mappage au format des messages de notification. Le code désérialise les messages reçus à partir de la file d'attente en objets du type **EncodingJobMessage** .
2. Charger les informations de compte Media Services et Storage à partir du fichier app.config. Utiliser ces informations pour créer les objets **CloudMediaContext** et **CloudQueue**.
3. Créer la file d’attente qui reçoit les messages de notification concernant le travail d’encodage.
4. Créer le point de terminaison de notification mappé à la file d’attente.
5. Associer le point de terminaison de notification à la tâche et soumettre la tâche d’encodage. Vous pouvez avoir plusieurs points de terminaison de notification associés à une tâche.
6. Dans cet exemple, comme seuls les derniers états du traitement du travail nous intéressent, nous transmettons **NotificationJobState.FinalStatesOnly** à la méthode **AddNew**.

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. Si vous transmettez NotificationJobState.All, vous obtiendrez des notifications de modification pour tous les états : En attente -> Planifié -> Traitement en cours -> Terminé. Toutefois, comme indiqué précédemment, le service de files d’attente de stockage Azure ne garantit pas une remise dans l’ordre d’arrivée. Vous pouvez utiliser la propriété Timestamp (définie sur le type EncodingJobMessage dans l’exemple ci-dessous) pour ordonner les messages. Il est possible que vous receviez des messages de notification en double. Utilisez la propriété ETag (définie sur le type EncodingJobMessage) pour rechercher les éventuels doublons. Il se peut également que certaines notifications de modification d’état soient ignorées.
8. Attendre que la tâche atteigne l’état Terminé en vérifiant la file d’attente toutes les 10 secondes. Supprimer les messages une fois qu’ils ont été traités.
9. Supprimer la file d’attente et le point de terminaison de notification.

> [!NOTE]
> La méthode recommandée pour surveiller l’état d’une tâche consiste à écouter les messages de notification, comme illustré dans l’exemple suivant.
>
> Vous pouvez également contrôler l'état d'une tâche à l'aide de la propriété **IJob.State** .  Il se peut qu’un message de notification annonçant la fin d’un travail arrive avant que l’état sur **IJob** soit défini sur **Terminé**. La propriété **IJob.State** reflète l’état correct avec un léger retard.
>
>

    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;

    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control.
            public String MessageVersion { get; set; }

            // Type of the event. Valid values are
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }

            // ETag is used to help the customer detect if
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }

            // Time of occurrence of the event.
            public String TimeStamp { get; set; }

            // Collection of values specific to the event.

            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint
            //          that triggered the notification.
            //     State- The state of the Endpoint.
            //          Valid values are: Registered and Unregistered.

            public IDictionary<string, object> Properties { get; set; }
        }

        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;

            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");

            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];


                string endPointAddress = Guid.NewGuid().ToString();

                // Create the context.
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);

                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);

                // Create the notification point that is mapped to the queue.
                _notificationEndPoint =
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }

                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }


            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();

                return queue;
            }


            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

                //Create an encrypted asset and upload the mp4.
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                    inputMediaFilePath);

                // Get a media processor reference, and pass to it the name of the
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task with the conversion details, using a configuration file.
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "H264 Multiple Bitrate 720p",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                    _notificationEndPoint);

                job.Submit();

                return job;
            }

            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;

                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;

                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));

                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                            Console.WriteLine();

                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }

                            // We are only interested in messages
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }

                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages,
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);

                        throw new TimeoutException();
                    }
                }
            }

            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                    assetCreationOptions);

                var fileName = Path.GetFileName(singleFilePath);

                var assetFile = asset.AssetFiles.Create(fileName);

                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);

                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);

                return asset;
            }

            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }
        }
    }

L’exemple ci-dessus produit le résultat suivant. Vos valeurs varieront.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>Étape suivante
Consulter les parcours d’apprentissage de Media Services

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Jan17_HO2-->


