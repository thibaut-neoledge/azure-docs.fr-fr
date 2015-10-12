## Téléchargement d’un fichier à partir d’un appareil simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans [Envoyer des messages du cloud vers des appareils avec IoT Hub] pour recevoir des messages du cloud vers des appareils à partir du concentrateur IoT.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **SimulatedDevice**, puis sur **Gérer les packages NuGet**. 

    La fenêtre Gérer les packages NuGet s’affiche.

2. Recherchez `WindowsAzure.Storage`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence au [Kit de développement logiciel (SDK) Microsoft Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. Au début du fichier **Program.cs**, ajoutez les instructions suivantes :

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Dans la classe **Program**, modifiez la méthode **ReceiveC2dAsync** de la façon suivante :
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    **ReceiveC2dAsync** différencie ainsi les messages comportant la propriété `command` ayant pour valeur `FileUpload`, qui seront gérés par la méthode **UploadFileToBlobAsync**.

    Ajoutez la méthode ci-dessous pour gérer les commandes de téléchargement de fichier.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Cette méthode utilise le Kit de développement logiciel (SDK) Azure Storage pour télécharger un objet blob de 10 Mo généré de manière aléatoire vers l’URI spécifié. Reportez-vous à [Azure Storage - Utilisation des objets blob] pour plus d’informations sur la façon de télécharger des objets blob.

> [AZURE.NOTE]Notez que cette implémentation de l’appareil simulé complète le message de l’appareil vers le cloud uniquement après que l’objet blob a été téléchargé. Cette approche simplifie le traitement des fichiers téléchargés dans le serveur principal, car l’accusé de livraison représente la disponibilité du fichier téléchargé à traiter. Toutefois, comme expliqué dans le [Guide du développeur IoT Hub][IoT Hub Developer Guide - C2D], un message qui n’est pas terminé avant le *délai de visibilité* (généralement 1 minute) est replacé dans la file d’attente et la méthode **ReceiveAsync()** le reçoit de nouveau. Pour les scénarios où le téléchargement du fichier peut prendre plus de temps, il peut être préférable que l’appareil simulé conserve un magasin durable des travaux en cours de téléchargement. Cela permet à l’appareil simulé de terminer le message du cloud vers l’appareil avant la fin du téléchargement du fichier, puis d’envoyer un message de l’appareil vers le cloud notifiant le serveur principal de la fin de l’opération.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure Storage - Utilisation des objets blob]: https://azure.microsoft.com/fr-FR/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=Oct15_HO1-->