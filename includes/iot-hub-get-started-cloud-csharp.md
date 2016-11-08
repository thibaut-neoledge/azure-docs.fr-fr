## Création d’une identité d’appareil
Dans cette section, vous allez créer une application console Windows qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **CreateDeviceIdentity**.
   
    ![][10]
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **CreateDeviceIdentity**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation.
   
    ![][11]
4. Cette opération lance le téléchargement, l’installation et ajoute une référence au package NuGet [kit de développement logiciel (SDK) de service Microsoft Azure IoT][lnk-nuget-service-sdk] et ses dépendances.
5. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
6. Ajoutez les champs suivants à la classe **Program**, en remplaçant la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section précédente :
   
        static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";
7. Ajoutez la méthode suivante à la classe **Program** :
   
        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }
   
    Cette méthode crée une nouvelle identité d’appareil avec l’ID **myFirstDevice** (si cet ID d’appareil existe déjà dans le registre, le code récupère simplement les informations d’appareil existantes). L’application affiche ensuite la clé primaire pour cette identité. Vous utiliserez cette clé dans l’appareil simulé pour vous connecter à votre IoT Hub.
8. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
9. Exécutez cette application et notez la clé de l’appareil.
   
    ![][12]

> [!NOTE]
> Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité. Par ailleurs, un indicateur activé/désactivé vous permet de désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.
> 
> 

## Recevoir des messages appareil-à-cloud
Dans cette section, vous allez créer une application console Windows qui lit les messages appareil-à-cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traitement de messages de type « Appareil vers cloud »][lnk-processd2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [!NOTE]
> Le point de terminaison compatible Event Hubs pour lire des messages de l'appareil vers le cloud utilise toujours le protocole AMQPS.
> 
> 

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **ReadDeviceToCloudMessages**.
   
    ![][10]
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadDeviceToCloudMessages**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la fenêtre **Gestionnaire de package NuGet**, recherchez **WindowsAzure.ServiceBus**, cliquez sur **Installer** et acceptez les conditions d’utilisation.
   
    Cette opération lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus][lnk-servicebus-nuget] avec toutes ses dépendances. Ce package permet à l'application de se connecter au point de terminaison compatible Event Hubs sur votre IoT hub.
4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.ServiceBus.Messaging;
        using System.Threading;
5. Ajoutez les champs suivants à la classe **Program**, en remplaçant la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section *Créer un IoT Hub* :
   
        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;
6. Ajoutez la méthode suivante à la classe **Program** :
   
        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;
   
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
   
    Cette méthode utilise une instance **EventHubReceiver** pour recevoir des messages à partir de toutes les partitions de réception Appareil vers cloud IoT Hub. Notez la manière dont vous transmettez un paramètre `DateTime.Now` lorsque vous créez l’objet **EventHubReceiver** pour qu’il reçoive uniquement les messages envoyés après son démarrage. Cette opération est utile dans un environnement de test, car elle vous permet de voir l’ensemble actuel de messages, mais dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, voir le didacticiel [Traiter les messages appareil-à-cloud IoT Hub][lnk-processd2c-tutorial].
7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);
   
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
   
        CancellationTokenSource cts = new CancellationTokenSource();
   
        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };
   
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0413_2016-->