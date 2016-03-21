## Approvisionnement d’un compte Azure Storage
Étant donné que l’appareil simulé télécharge un fichier dans un objet blob Azure Storage, vous devez disposer d’un compte Azure Storage. Vous pouvez utiliser un compte existant, ou suivre les instructions figurant dans [À propos d’Azure Storage] pour en créer un. Notez la chaîne de connexion du compte de stockage.

## Envoi d’un URI d’objet blob Azure à l’appareil simulé

Dans cette section, vous allez modifier l’application console **SendCloudtoDevice** que vous avez créée dans [Envoyer des messages cloud-à-appareil avec IoT Hub] pour inclure un URI d’objet blob Azure avec une signature d’accès partagé. Cela permet au serveur cloud principal d’accorder l’accès en écriture à l’objet blob uniquement au destinataire du message cloud-à-appareil.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **SendCloudtoDevice**, puis sur **Gérer les packages NuGet**. 

    La fenêtre Gérer les packages NuGet s’affiche.

2. Recherchez `WindowsAzure.Storage`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence au [Kit de développement logiciel (SDK) Microsoft Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. Au début du fichier **Program.cs**, ajoutez les instructions suivantes :

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Dans la classe **Program**, ajoutez les champs suivants de la classe, en remplaçant la chaîne de connexion pour votre compte de stockage.

        static string storageConnectionString = "{storage connection string}";

    Ensuite, ajoutez la méthode suivante (vous pouvez remplacer n’importe quel nom de conteneur d’objets blob, ce didacticiel utilise **iothubfileuploadtutorial**) :
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Cette méthode crée une référence d’objet blob et génère un URI de signature d’accès partagé, comme décrit dans [Créer et utiliser une signature d’accès partagé avec Blob Storage](../storage/storage-dotnet-shared-access-signature-part-2.md). Notez que la méthode ci-dessus génère un URI de signature qui est valide pendant 24 heures. Si l’appareil cible nécessite plus de temps pour télécharger le fichier (par exemple, il se connecte rarement, il dispose d’une connectivité qui n’est pas fiable pour télécharger un fichier volumineux), vous pouvez envisager des délais d’expiration plus longs pour les signatures.

5. Modifiez **SendCloudToDeviceMessageAsync** de la façon suivante :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Cette méthode envoie un message cloud-à-appareil qui contient deux propriétés d’application : une qui identifie ce message en tant que commande pour télécharger un fichier, l’autre contenant l’URI d’objet blob. Il demande également des accusés de livraison complets. Notez que les informations contenues dans les propriétés des deux applications peuvent être sérialisées dans un corps de message, mais cela impliquerait un traitement supplémentaire pour sérialiser et désérialiser les informations.

<!-- Links -->

[À propos d’Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!------HONumber=AcomDC_0309_2016-->