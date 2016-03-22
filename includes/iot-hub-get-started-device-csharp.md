## Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application console Windows qui simule un appareil envoyant des messages Appareil vers cloud à un IoT Hub.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Nommez le projet **SimulatedDevice**.

   	![][30]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulatedDevice**, puis cliquez sur **Gérer les packages NuGet**.

3. Dans la fenêtre **Gestionnaire de package NuGet**, recherchez **Client d’appareils Microsoft Azure**, cliquez sur **Installer**et acceptez les conditions d’utilisation.

	Cette opération lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT - Device SDK NuGet][lnk-device-nuget].

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant les valeurs des espaces réservés par le nom d’hôte IoT Hub figurant dans la section *Créer un IoT Hub* et par la clé d’appareil figurant dans la section *Créer une identité d’appareil* :

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Ajoutez la méthode suivante à la classe **Program** :

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Thread.Sleep(1000);
            }
        }

	Cette méthode envoie un nouveau message Appareil vers cloud toutes les secondes. Le message contient un objet JSON sérialisé avec l’ID de l’appareil et un nombre généré de manière aléatoire pour simuler un anémomètre.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Par défaut, la méthode de création **Create** crée un client d’appareil **DeviceClient** qui utilise le protocole AMQP pour communiquer avec IoT Hub. Pour utiliser le protocole HTTPS, remplacez la méthode **Create** afin de pouvoir spécifier le protocole. Si vous choisissez d’utiliser le protocole HTTPS, vous devez également ajouter le package NuGet **Microsoft.AspNet.WebApi.Client** à votre projet de manière à inclure l’espace de noms **System.Net.Http.Formatting**.


> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---------HONumber=AcomDC_0309_2016-->