## Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez écrire une application console Windows qui reçoit des messages de notification de téléchargement de fichier à partir d’IoT Hub.

1. Dans la solution Visual Studio actuelle, créez un projet Windows Visual C# à l’aide du modèle de projet d’**application de console**. Nommez le projet **ReadFileUploadNotification**.

    ![Nouveau projet dans Visual Studio][2]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadFileUploadNotification**, puis cliquez sur **Gérer les packages NuGet**.

    La fenêtre Gérer les packages NuGet s’affiche.

2. Recherchez `Microsoft.Azure.Devices`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

	Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure IoT - Service SDK NuGet] dans le projet **ReadFileUploadNotification**.

3. Au début du fichier **Program.cs**, ajoutez les instructions suivantes :

        using Microsoft.Azure.Devices;

4. Ajoutez les champs suivants à la classe **Program** : Remplacez la valeur d’espace réservé par la chaîne de connexion du concentrateur IoT de la section [Prise en main de IoT Hub] :

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Ajoutez la méthode suivante à la classe **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

6. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[package Azure IoT - Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Prise en main de IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->