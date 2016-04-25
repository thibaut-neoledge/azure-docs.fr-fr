## Traitement des messages appareil-à-cloud

Dans cette section, vous allez créer une application de console Windows qui traite les messages appareil-à-cloud à partir d’IoT Hub. IoT Hub expose un point de terminaison compatible avec [Event Hubs] pour permettre à une application de lire les messages appareil-à-cloud. Ce didacticiel utilise la classe [EventProcessorHost] pour traiter ces messages dans une application de console. Pour plus d’informations sur la façon de traiter les messages à partir des concentrateurs d’événements, vous pouvez vous reporter au didacticiel [Prise en main des concentrateurs d’événements].

Le défi principal auquel vous êtes confronté lors de l’implémentation d’un stockage fiable des messages de point de données ou du transfert des messages interactifs, est de faire en sorte que le traitement des événements Event Hubs s’appuie sur le consommateur de message pour contrôler la progression. En outre, pour atteindre un débit élevé, lors de la lecture à partir d’Event Hubs, vous devez effectuer les contrôles par grands lots, créant ainsi une possibilité de traitement en double pour un grand nombre de messages en cas d’erreur où vous devriez revenir au point de contrôle précédent. Dans ce didacticiel, vous verrez comment synchroniser des écritures Azure Storage et des fenêtres de déduplication Service Bus avec les points de contrôle d’**EventProcessorHost**.

Pour écrire des messages dans Azure Storage de manière fiable, l’exemple utilise la fonctionnalité de validation individuelle des blocs des [objets blob de blocs][Azure Block Blobs]. Le processeur d’événements accumule les messages dans la mémoire, jusqu’au moment du contrôle, c’est-à-dire une fois que la mémoire tampon accumulée de messages dépasse la taille de bloc maximale de 4 Mo, ou après écoulement de la fenêtre de temps de déduplication Service Bus. Ensuite, avant d’effectuer le contrôle, le code valide un nouveau bloc dans l’objet blob.

Le processeur d’événements utilise les décalages des messages des hubs d’événements comme ID de bloc. Cela lui permet d’effectuer une vérification de la déduplication avant de valider le nouveau bloc dans le stockage, en tenant compte d’un possible incident entre un bloc et le point de contrôle.

> [AZURE.NOTE] Ce didacticiel utilise un même compte de stockage pour enregistrer tous les messages récupérés d’IoT Hub. Reportez-vous aux [Instructions d’évolutivité d’Azure Storage] pour décider si vous avez besoin d’utiliser plusieurs comptes Azure Storage dans votre solution.

L’application utilise la fonctionnalité de déduplication de Service Bus afin d’éviter les doublons lorsqu’elle traite les messages interactifs. L’appareil simulé marque chaque message interactif avec une propriété **MessageId** unique pour permettre à Service Bus de garantir que la fenêtre de temps de déduplication spécifiée ne remettra jamais deux messages possédant la même propriété **MessageId** aux récepteurs. Cette déduplication, conjointement avec la sémantique d’achèvement par message fournie par les files d’attente Service Bus, facilite le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé en dehors de la fenêtre de déduplication, le code synchronise le mécanisme de contrôle d’**EventProcessorHost** avec la fenêtre de déduplication de la file d’attente Service Bus. Pour cela, vous devez forcer un point de contrôle au moins une fois par écoulement de fenêtre de déduplication (une heure dans ce didacticiel).

> [AZURE.NOTE] Ce didacticiel utilise une file d’attente Service Bus partitionnée unique pour traiter tous les messages interactifs récupérés d’IoT Hub. Reportez-vous à la [documentation de Service Bus] pour plus d’informations sur l’utilisation des files d’attente Service Bus pour répondre aux exigences d’évolutivité de votre solution.

### Approvisionner un compte Azure Storage et une file d’attente Service Bus
Pour pouvoir utiliser la classe [EventProcessorHost], vous devez disposer d’un compte Azure Storage pour permettre à l’**EventProcessorHost** d’enregistrer les informations de point de contrôle. Vous pouvez utiliser un compte de stockage existant ou suivre les instructions figurant dans [À propos d’Azure Storage] pour en créer un. Notez la chaîne de connexion du compte de stockage.

> [AZURE.NOTE] Lorsque vous copiez et collez la chaîne de connexion du compte de stockage, assurez-vous que la chaîne de connexion ne contient aucun espace.

Vous avez également besoin d’une file d’attente Service Bus pour permettre un traitement fiable des messages interactifs. Vous pouvez créer une file d’attente par programmation avec une fenêtre de déduplication de 1 heure, comme expliqué dans [Utilisation des files d’attente Service Bus][Service Bus Queue]. Vous pouvez également utiliser le [portail Azure Classic] en procédant comme suit :

1. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, puis sur **App Services**, **Service Bus**, **File d’attente** et **Création personnalisée**. Entrez le nom **d2ctutorial**, sélectionnez une région, utilisez un espace de noms existant ou créez-en un nouveau. Ensuite, sur la page suivante, sélectionnez **Activer la détection dupliquée** et définissez l’option **Fenêtre d’heures de l’historique des détections dupliquées** sur une heure. Cliquez ensuite sur la coche pour enregistrer votre configuration de la file d’attente.

    ![][30]

2. Dans la liste des files d’attente Service Bus, cliquez sur **d2ctutorial**, puis sur **Configurer**. Créez deux stratégies d’accès partagé, une appelée **send** avec les autorisations **Send** et une autre nommée **listen** avec les autorisations **Listen**. Cliquez sur **Enregistrer** en bas lorsque vous avez terminé.

    ![][31]

3. Cliquez sur **Tableau de bord** en haut, puis sur **Informations de connexion** en bas, et notez les deux chaînes de connexion.

    ![][32]

### Créer le processeur d’événements

1. Dans la solution Visual Studio actuelle, cliquez sur **Fichier**, puis sur **Ajouter** et **Nouveau projet** pour créer un projet Windows Visual C# à l’aide du modèle de projet d’**application de console**. Vérifiez que la version de .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **ProcessDeviceToCloudMessages**.

    ![][10]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProcessDeviceToCloudMessages**, puis cliquez sur **Gérer les packages NuGet**. La boîte de dialogue **Gestionnaire de packages NuGet** apparaît.

3. Recherchez **WindowsAzure.ServiceBus**, cliquez sur **Installer** et acceptez les conditions d’utilisation. Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) avec toutes ses dépendances.

4. Recherchez **Microsoft Azure Service Bus Event Hub - EventProcessorHost**, cliquez sur **Installer** et acceptez les conditions d’utilisation. Cette opération lance le téléchargement, l'installation et ajoute une référence au [Package NuGet Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), avec toutes les dépendances associées.

5. Cliquez avec le bouton droit sur le projet **ProcessDeviceToCloudMessages**, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **StoreEventProcessor**, puis cliquez sur **OK** pour créer la classe.

6. Ajoutez les instructions ci-après au début du fichier StoreEventProcessor.cs :

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Insérez le code suivant dans le corps de la classe :

    ```
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
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

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
    ```

    La classe **EventProcessorHost** appelle cette classe pour traiter les messages appareil-à-cloud reçus d’IoT Hub. Le code dans cette classe implémente la logique, afin de stocker les messages de manière fiable dans un conteneur d’objets blob et de transférer les messages interactifs à la file d’attente Service Bus. La méthode **OpenAsync** initialise la variable **currentBlockInitOffset** qui suit le décalage actuel du premier message lu par ce processeur d’événements. N’oubliez pas : chaque processeur est chargé d’une partition unique.
    
    La méthode **ProcessEventsAsync** reçoit un lot de messages d’IoT Hub et les traite comme suit : elle envoie des messages interactifs à la file d’attente Service Bus et ajoute les messages de point de données à la mémoire tampon appelée **toAppend**. Si la mémoire tampon atteint la limite de bloc de 4 Mo ou si les fenêtres de temps de déduplication Service Bus se sont écoulées depuis le dernier point de contrôle (une heure dans ce didacticiel), un point de contrôle est déclenché.

    La méthode **AppendAndCheckpoint** génère d’abord un ID de bloc pour le bloc à ajouter. Azure Storage requiert que tous les ID de bloc aient la même longueur, la méthode remplit donc le décalage avec des zéros non significatifs - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Ensuite, si un bloc avec cet ID se trouve déjà dans l’objet blob, la méthode le remplace par le contenu actuel de la mémoire tampon.

    > [AZURE.NOTE] Pour simplifier le code, ce didacticiel utilise un fichier blob unique par partition pour stocker les messages. La solution serait d’implémenter une substitution de fichier, en créant des fichiers supplémentaires lorsqu’ils atteignent une certaine taille (notez qu’un objet blob de blocs Azure est limité à 195 Go), ou après un certain laps de temps.

8. Dans la classe **Programme**, ajoutez les instructions **using** suivantes en haut :

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifiez la méthode **Main** dans la classe **Program**, comme illustré ci-dessous, en remplaçant la chaîne de connexion d’IoT Hub **iothubowner** (voir le didacticiel [Prise en main d’IoT Hub]), la chaîne de connexion de stockage et la chaîne de connexion Service Bus par des autorisations **Send** pour la file d’attente nommée **d2ctutorial** :

    ```
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
    ```
    
    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel utilise une seule instance de la classe [EventProcessorHost]. Reportez-vous au [Guide de programmation Event Hubs] pour plus d’informations.

## Recevoir des messages interactifs
Dans cette section, vous écrirez une application de console Windows qui reçoit les messages interactifs à partir de la file d’attente Service Bus. Pour plus d’informations sur la construction d’une solution à l’aide de Service Bus, consultez [Créer des applications multiniveaux avec Service Bus][].

1. Dans la solution Visual Studio actuelle, créez un projet Windows Visual C# à l’aide du modèle de projet d’**application de console**. Nommez le projet **ProcessD2CInteractiveMessages**.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProcessD2CInteractiveMessages**, puis cliquez sur **Gérer les packages NuGet**. Cela affiche la fenêtre **Gestionnaire de packages NuGet**.

3. Recherchez **WindowsAzure.Service Bus**, cliquez sur **Installer** et acceptez les conditions d’utilisation. Cette opération lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) avec toutes ses dépendances.

4. Ajoutez l’instruction **using** suivante au début du fichier **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Enfin, ajoutez les lignes suivantes à la méthode **Main**, en remplaçant la chaîne de connexion par des autorisations **Listen** pour la file d’attente nommée **d2ctutorial** :

    ```
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
    ```

<!-- Links -->
[À propos d’Azure Storage]: ../articles/storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Prise en main des concentrateurs d’événements]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[Instructions d’évolutivité d’Azure Storage]: ../articles/storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../articles/event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guide de programmation Event Hubs]: ../articles/event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://manage.windowsazure.com/
[Service Bus Queue]: ../articles/service-bus/service-bus-dotnet-how-to-use-queues.md
[Créer des applications multiniveaux avec Service Bus]: ../articles/service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[Prise en main d’IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[documentation de Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0413_2016-->