## Création d’une application de périphérique simulé

Dans cette section, vous allez écrire une application console Windows qui simule un appareil envoyant des messages appareil-cloud à un hub IoT.

1. Dans la solution Visual Studio actuelle, cliquez sur **Fichier->Ajouter->Projet** pour créer un projet d’application de bureau Visual C# à l'aide du modèle de projet d'**application de console**. Nommez le projet **SimulatedDevice**.

   	![][30]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**.

	La fenêtre Gérer les packages NuGet s’affiche.

3. Recherchez `Microsoft Azure Devices Client`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

	Cette opération lance le téléchargement, l'installation et ajoute une référence au [package Azure IoT - Device SDK NuGet].

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant les valeurs des espaces réservés par l’URI du hub IoT et par la clé d’appareil figurant dans les sections **Créer un hub IoT** et **Créer une identité d'appareil**, respectivement :

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

6. Ajoutez la méthode suivante à la classe **Program** :

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

	Cette méthode enverra chaque seconde un nouveau message appareil-cloud contenant un objet JSON sérialisé avec l'ID de l’appareil et un nombre généré de manière aléatoire, représentant un anémomètre simulé.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Par défaut, la méthode **Create (créer)** crée un **DeviceClient** qui utilise le protocole AMQP pour communiquer avec le concentrateur IoT. Pour utiliser le protocole HTTPS, remplacez la méthode **Create** pour spécifier le protocole. Si vous choisissez d'utiliser le protocole HTTPS, vous devez également ajouter le package NuGet **Microsoft.AspNet.WebApi.Client** à votre projet de manière à inclure l'espace de noms **System.Net.Http.Formatting**.


> [AZURE.NOTE]Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Il est recommandé de mettre en œuvre dans le code de production des stratégies de nouvelle tentative (comme par exemple, l'interruption exponentielle), comme indiqué dans l'article MSDN [Gestion des erreurs temporaires].

<!-- Links -->

[package Azure IoT - Device SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Gestion des erreurs temporaires]: https://msdn.microsoft.com/fr-FR/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Nov15_HO2-->