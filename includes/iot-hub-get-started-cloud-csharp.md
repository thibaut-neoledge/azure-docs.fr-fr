## Création d’une identité d’appareil

Dans cette section, vous allez créer une application console Windows qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages Appareil vers cloud à IoT Hub.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Nommez le projet **CreateDeviceIdentity**.

	![][10]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **CreateDeviceIdentity**, puis cliquez sur **Gérer les packages NuGet**.

3. Dans la fenêtre **Gestionnaire de packages NuGet**, assurez-vous que l’option **Inclure la version préliminaire** est activée. Ensuite, recherchez **Appareils Microsoft Azure**, cliquez sur **Installer** et acceptez les conditions d’utilisation.

	![][11]

4. Cette opération lance le téléchargement, l’installation et ajoute une référence au package NuGet de [kit de développement logiciel Microsoft Azure Devices][lnk-nuget-device-sdk].

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section précédente :

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

	Cette méthode crée une nouvelle identité d’appareil avec l’ID **myFirstDevice** (si cet ID d’appareil existe déjà dans le registre, le code récupère simplement les informations d’appareil existantes). L’application affiche ensuite la clé primaire pour cette identité. Vous utiliserez cette clé dans l’appareil simulé pour vous connecter à votre IoT Hub.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Exécutez cette application et notez la clé de l’appareil.

    ![][12]

> [AZURE.NOTE]Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité. Par ailleurs, un indicateur activé/désactivé vous permet de désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.

## Recevoir des messages des appareils vers le cloud

Dans cette section, vous allez créer une application console Windows qui lit les messages Appareil vers cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traitement de messages de type « Appareil vers cloud »][lnk-processd2c-tutorial] vous montre comment traiter les messages Appareil vers cloud à grande échelle, tandis que le didacticiel [Prise en main d’Event Hubs][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Nommez le projet **ReadDeviceToCloudMessages**.

    ![][10]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadDeviceToCloudMessages**, puis cliquez sur **Gérer les packages NuGet**.

3. Dans la fenêtre **Gestionnaire de packages NuGet**, assurez-vous que l’option **Inclure la version préliminaire** est activée. Recherchez ensuite **WindowsAzure.ServiceBus**, cliquez sur **Installer** et acceptez les conditions d’utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus][lnk-servicebus-nuget] avec toutes ses dépendances.

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

        using Microsoft.ServiceBus.Messaging;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section *Créer un IoT Hub* :

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

    Cette méthode utilise une instance **EventHubReceiver** pour recevoir des messages à partir de toutes les partitions de réception Appareil vers cloud IoT Hub. Notez la manière dont vous transmettez un paramètre `DateTime.Now` lorsque vous créez l’objet **EventHubReceiver** pour qu’il reçoive uniquement les messages envoyés après son démarrage.

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

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: event-hubs-overview.md

[lnk-nuget-device-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_1217_2015-->