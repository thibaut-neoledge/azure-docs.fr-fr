## Traitement des messages des appareils vers le cloud

Dans cette section, vous allez créer une application console Windows qui traite les messages des appareils vers le cloud à partir d’IoT Hub. IOT Hub expose un point de terminaison compatible avec les [concentrateurs d’événements][Event Hubs Overview] pour lire les messages des appareils vers le cloud. Ce didacticiel utilise [EventProcessorHost] pour traiter ces messages dans une application console. Pour plus d’informations sur la façon de traiter les messages à partir des concentrateurs d’événements, vous pouvez vous reporter au didacticiel [Prise en main des concentrateurs d’événements].

Le défi principal lors de l’implémentation d’un stockage fiable des messages de point de données ou du transfert des messages interactifs, est de faire en sorte que le traitement des événements des concentrateurs d’événements s’appuie sur le consommateur de message pour contrôler la progression. En outre, pour atteindre un débit élevé, lors de la lecture à partir des concentrateurs d’événements, les contrôles doivent être effectués dans les grands lots, créant une possibilité de traitement en double pour un grand nombre de messages. Dans ce didacticiel, nous montrons comment synchroniser des écritures Azure Storage et des fenêtres de déduplication Service Bus avec les points de contrôle de l’hôte du processeur d’événements.

Pour écrire des messages dans Azure Storage de manière fiable, nous utilisons la fonctionnalité [blobs de blocs][Azure Block Blobs] de validation individuelle des blocs. Le processeur d’événements accumule les messages dans la mémoire, jusqu’au moment du contrôle (c’est-à-dire une fois que la mémoire tampon accumulée a dépassé la taille de bloc maximale de 4 Mo, ou après écoulement des fenêtres de temps de déduplication Service Bus). Ensuite, avant d’effectuer le contrôle, un nouveau bloc est présenté au blob pour validation.

Le processeur d’événements utilise les décalages des messages des concentrateurs d’événements comme ID de bloc. Cela permet d’effectuer une vérification de la déduplication avant de valider le nouveau bloc dans le stockage, en tenant compte d’un possible incident entre un bloc validé et le point de contrôle.

> [AZURE.NOTE]Ce didacticiel utilise un même compte de stockage pour enregistrer tous les messages récupérés d’IoT Hub. Reportez-vous aux [Instructions d’évolutivité d’Azure Storage] pour décider si vous avez besoin de plusieurs comptes Azure Storage dans votre solution.

Afin d’éviter les doublons dans le traitement des messages interactifs, nous utilisons la fonctionnalité de déduplication Service Bus. Par le marquage de chaque message interactif avec un `MessageId` unique, Service Bus peut garantir que la fenêtre de temps de déduplication spécifiée ne remettra jamais deux messages possédant le même `MessageId` aux récepteurs. Cette déduplication, conjointement avec la sémantique d’achèvement par message fournie par les files d’attente Service Bus, facilite considérablement le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé en dehors de la fenêtre de déduplication, nous synchronisons le mécanisme de contrôle de l’hôte du processeur d’événements avec la fenêtre de déduplication de la file d’attente Service Bus. Pour cela, vous devez forcer un point de contrôle au moins une fois par écoulement de fenêtre (dans ce didacticiel, 1 heure).

> [AZURE.NOTE]Ce didacticiel utilise une file d’attente Service Bus partitionnée unique pour traiter tous les messages interactifs récupérés d’IoT Hub. En fonction des besoins de votre solution, reportez-vous à la [documentation de Service Bus] pour plus d’informations sur l’utilisation des files d’attente Service Bus.

### Approvisionner un compte Azure Storage et une file d’attente Service Bus
Pour utiliser [EventProcessorHost], vous devez disposer d’un compte Azure Storage. Vous pouvez utiliser un compte existant, ou suivre les instructions figurant dans [À propos d’Azure Storage] pour en créer un. Notez la chaîne de connexion du compte de stockage.

Vous aurez également besoin d’une file d’attente Service Bus pour permettre un traitement fiable des messages interactifs. Vous pouvez créer une file d’attente par programme avec une fenêtre de déduplication d’1 heure, comme expliqué dans [Utilisation des files d’attente Service Bus][Service Bus Queue], ou utiliser le [portail de gestion Azure], en procédant comme suit :

1. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, puis sur **App Services**, puis **Service Bus**, puis **File d’attente**, puis **Personnalisé**. Sélectionnez ensuite le nom **d2ctutorial**, puis choisissez une fenêtre de déduplication d’1 heure.

    ![][30]

2. Cliquez sur le nom de la file d’attente (**d2ctutorial**), puis sur **Configurer**, et créez deux stratégies d’accès partagé, une appelée **send** avec les autorisations **Send** et une autre nommée **listen** avec les autorisations **Listen**. Cliquez sur **Enregistrer** en bas lorsque vous avez terminé.

    ![][31]

3. Cliquez sur **Tableau de bord** en haut, puis sur **Informations de connexion** en bas, et notez les deux chaînes de connexion.

    ![][32]

### Créer le processeur d’événements

1. Dans la solution Visual Studio actuelle, cliquez sur **Fichier->Ajouter->Projet** pour créer un projet d’application de bureau Visual C# à l’aide du modèle de projet d’**application console**. Nommez le projet **ProcessDeviceToCloudMessages**.

    ![][10]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**.

    La boîte de dialogue **Gérer les packages NuGet** apparaît.

3. Recherchez `WindowsAzure.ServiceBus`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) avec toutes ses dépendances.

4. Recherchez `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure Service Bus Event Hub - EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), avec toutes les dépendances associées.

5. Cliquez avec le bouton droit sur le projet **ProcessDeviceToCloudMessages**, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **StoreEventProcessor**, puis cliquez sur **OK** pour créer la classe.

6. Ajoutez les instructions ci-après au début du fichier SimpleEventProcessor.cs :

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    Insérez ensuite le code suivant dans le corps de la classe :

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }
                    
                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    Cette classe est appelée par le **EventProcessorHost** pour traiter les messages des appareils vers le cloud provenant d’IoT Hub et implémente la logique principale pour le stockage fiable des messages dans un conteneur d’objets blob et le transfert des messages interactifs vers la file d’attente Service Bus. La méthode `OpenAsync()` initialise la variable `currentBlockInitOffset` qui effectue le suivi du décalage actuel du premier message lu par ce processeur d’événements (souvenez-vous que chaque processeur est chargé d’une partition unique).

    La méthode `ProcessEventsAsync()` reçoit un lot de messages d’IoT Hub qui sont traités de la manière suivante. Les messages interactifs sont envoyés à la file d’attente Service Bus, tandis que les messages de point de données sont ajoutés à la mémoire tampon appelée `toAppend`. Si la mémoire tampon atteint la limite de bloc (c’est-à-dire 4 Mo) ou si les fenêtres de temps de déduplication Service Bus se sont écoulées depuis le dernier point de contrôle (dans ce didacticiel, 1 heure), un point de contrôle est déclenché.

    La méthode `AppendAndCheckpoint()` génère d’abord un ID de bloc pour le bloc à ajouter. Dans la mesure où Azure Storage requiert que tous les ID de bloc aient la même longueur, le décalage est rempli avec des zéros non significatifs (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Ensuite, si un bloc avec cet ID se trouve déjà dans le blob, la méthode le remplace par le bloc en cours.

> [AZURE.NOTE]Pour simplifier le code, ce didacticiel utilise un fichier blob unique par partition pour stocker les messages. La solution serait d’implémenter une substitution de fichier, en créant des fichiers supplémentaires lorsqu’ils atteignent une certaine taille (notez qu’un blob Azure est limité à 195 Go), ou après un certain laps de temps (par exemple, `fileName_{partitionId}_{date-time}`).

7. Dans la classe **Program**, ajoutez les instructions `using` suivantes en haut :

        using Microsoft.ServiceBus.Messaging;

    Ensuite, modifiez la méthode **Main** pour lui affecter la classe **Programme**, comme illustré ci-dessous, en remplaçant la chaîne de connexion d’IoT Hub **iothubowner** (voir didacticiel [Prise en main d’IoT Hub]), la chaîne de connexion de stockage et la chaîne de connexion Service Bus par des autorisations **Send** pour la file d’attente nommée **d2ctutorial** :

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]Par souci de simplicité, ce didacticiel utilise une seule instance d’[EventProcessorHost]. Veuillez vous reporter au [Guide de programmation des concentrateurs d’événements] et au didacticiel [Traiter les messages des appareils vers le cloud] pour plus d’informations sur le traitement des messages des appareils vers le cloud.

## Recevoir des messages interactifs
Dans cette section, vous écrirez une application console Windows qui reçoit les messages interactifs à partir de la file d’attente Service Bus. Pour plus d’informations sur la construction d’une solution à l’aide de Service Bus, consultez [Créer des applications multiniveaux avec Service Bus].

1. Dans la solution Visual Studio actuelle, créez un projet d’application de bureau Visual C# à l’aide du modèle de projet d’**application console**. Nommez le projet **ProcessD2cInteractiveMessages**.

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**.

    La fenêtre Gérer les packages NuGet s’affiche.

3. Recherchez `WindowsAzure.Service Bus`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) avec toutes ses dépendances.

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Enfin, ajoutez les lignes suivantes à la méthode **Main**, en remplaçant la chaîne de connexion par des autorisations **Listen** pour la file d’attente nommée **d2ctutorial** :

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[À propos d’Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Prise en main des concentrateurs d’événements]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Instructions d’évolutivité d’Azure Storage]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[documentation de Service Bus]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Guide de programmation des concentrateurs d’événements]: ../event-hubs/event-hubs-programming-guide.md

[Azure Preview Portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[portail de gestion Azure]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=Oct15_HO1-->