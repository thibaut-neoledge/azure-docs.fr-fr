## Création d’une identité d’appareil

Dans cette section, vous écrirez une application console Windows qui crée une identité d’appareil dans votre concentrateur IoT. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][IoT Hub Developer Guide - Identity Registry] pour plus d’informations. Après avoir exécuté cette application console, vous obtenez un ID et une clé à utiliser comme identité de l’appareil pour envoyer des messages de l’appareil vers le cloud vers IoT HUB.

1. Dans Visual Studio, créez un projet d'application de bureau Visual C# à l'aide du modèle de projet d'**application de console**. Nommez le projet **CreateDeviceIdentity**.

	![][10]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**.

	La fenêtre **NuGet Package Manager** s’ouvre.

3. Assurez-vous que l’option **Inclure la version préliminaire** est activée. Ensuite, recherchez `Microsoft Azure Devices`, cliquez sur **Installer** et acceptez les conditions d’utilisation.

	![][11]

4. Cette opération lance le téléchargement, l’installation et ajoute une référence au package NuGet de [kit de développement logiciel Microsoft Azure Devices](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant les valeurs d’espace réservé par le nom du concentrateur IoT que vous avez créé dans la section précédente et sa chaîne de connexion :

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

		private async static Task AddDeviceAsync()
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

	Cette méthode crée une identité d’appareil avec l’ID **myFirstDevice** (au cas où une identité existe déjà avec le même ID, elle est simplement récupérée). Ensuite, l’application affiche la clé primaire de cette identité. Cette clé est utilisée par l’appareil simulé pour se connecter à IoT Hub.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Exécutez cette application et notez la clé de l’appareil.

    ![][12]

> [AZURE.NOTE]Il est important de noter que le Registre d’identité du concentrateur IoT est uniquement utilisé pour stocker les identités des appareils en vue d’un accès sécurisé, par exemple, stocker les informations d’identification de sécurité et activer/désactiver l’accès à l’appareil individuel. Les métadonnées de l’application de l’appareil doivent être stockées dans un magasin spécifique à l’application. Reportez-vous au [Guide du développeur IoT Hub][IoT Hub Developer Guide - Identity Registry] pour plus d’informations.

## Recevoir des messages des appareils vers le cloud

Dans cette section, vous allez créer une application console Windows qui lit les messages des appareils vers le cloud à partir d’IoT Hub. IOT Hub présente un point de terminaison compatible avec [Event Hubs][Event Hubs Overview] pour lire les messages appareils vers cloud. Par souci de simplicité, ce didacticiel crée un lecteur simplifié qui ne convient pas dans le cas d’un déploiement à débit élevé. Vous trouverez plus d’informations sur la façon de traiter les messages des appareils vers le cloud d’IoT Hub dans le didacticiel [Traiter les messages des appareils vers le cloud]. Pour plus d’informations sur la façon de traiter les messages à partir d’Event Hubs, vous pouvez vous reporter au didacticiel [Prise en main d’Event Hubs].

1. Dans la solution Visual Studio actuelle, cliquez sur **Fichier->Ajouter->Projet** pour créer un projet d’application de bureau Visual C# à l’aide du modèle de projet **Application console**. Nommez le projet **ReadDeviceToCloudMessages**.

    ![][10]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**.

    La boîte de dialogue **Gérer les packages NuGet** apparaît.

3. Recherchez `WindowsAzure.ServiceBus`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) avec toutes ses dépendances.

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

        using Microsoft.ServiceBus.Messaging;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant les valeurs d’espace réservé par le nom du concentrateur IoT créé dans la section précédente et sa chaîne de connexion :

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Ajoutez la méthode suivante à la classe **Program** :

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Cette méthode utilise un client EventHub pour recevoir toutes les partitions de réception des messages des appareils vers le cloud de votre concentrateur IoT. Notez comment, lors de la création d’un EventHubReceiver, un paramètre `DateTime.Now` est transmis. Cela crée un destinataire qui recevra uniquement les messages qui sont envoyés après son démarrage.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Prise en main d’Event Hubs]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure Preview Portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Oct15_HO3-->