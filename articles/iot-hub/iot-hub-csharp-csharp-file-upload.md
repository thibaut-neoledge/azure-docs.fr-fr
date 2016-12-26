---
title: "Charger des fichiers à partir d’appareils à l’aide d’IoT Hub | Microsoft Docs"
description: "Suivez ce didacticiel pour découvrir comment télécharger des fichiers à partir d’appareils à l’aide d’Azure IoT Hub avec C#."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: 2310e7eab7d2649f91264798ad59cd4cfe92fa96


---
# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Didacticiel : télécharger des fichiers à partir d’appareils vers le cloud avec IoT Hub
## <a name="introduction"></a>Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur d’applications principal. Les didacticiels précédents ([Prise en main d’IoT Hub] et [Envoi de messages cloud-à-appareil avec IoT Hub]) illustre les fonctionnalités de base de messagerie appareil-à-cloud et cloud-à-appareil offertes par IoT Hub. Le didacticiel [Traiter les messages appareil-à-cloud] décrit un moyen de stocker en toute fiabilité les messages appareil-à-cloud dans le stockage d’objets blob Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Il s’agit par exemple de fichiers volumineux contenant des images, des vidéos, des exemples de données de vibration à haute fréquence ou contenant une forme quelconque de données prétraitées. Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory] ou de la pile [Hadoop]. Quand le téléchargement d’un fichier à partir d’un appareil est préférable à l’envoi d’événements, vous pouvez néanmoins utiliser les fonctionnalités de fiabilité et de sécurité offertes par IoT Hub.

Ce didacticiel s’appuie sur le code du didacticiel [Envoi de messages cloud-à-appareil avec IoT Hub] pour vous montrer comment utiliser les fonctions de téléchargement de fichier d’IoT Hub. Cette rubrique vous explique les procédures suivantes :

- Fournissez en toute sécurité à un appareil un URI d’objet blob Azure pour le chargement d’un fichier.
- Utilisez les notifications de chargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur principal d’application.

À la fin de ce didacticiel, vous exécuterez deux applications de console Windows :

* **SimulatedDevice**, version modifiée de l’application créée dans le cadre du didacticiel [Envoi de messages cloud-à-appareil avec IoT Hub]. Cette application charge un fichier de stockage à l’aide d’un URI SAP fourni par votre IoT Hub.
* **ReadFileUploadNotification**, qui reçoit les notifications de téléchargement de fichier à partir de votre IoT Hub.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT].
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Microsoft Visual Studio 2015
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associer un compte Azure Storage à IoT Hub
Étant donné que l’application d’appareil simulé charge un fichier dans un objet Blob, vous devez disposer d’un compte [Azure Storage] associé à IoT Hub. Lorsque vous associez un compte Stockage Azure à un IoT Hub, le concentrateur peut générer un URI SAP qu’un appareil peut utiliser pour charger en toute sécurité un fichier vers un conteneur d’objets blob. Le service IoT Hub et les Kits de développement logiciel (SDK) d’appareil coordonnent le processus qui génère l’URI SAS et le rend disponible pour un appareil à utiliser pour charger un fichier.

Pour associer un compte Azure Storage à votre IoT Hub, suivez les instructions de [Configurer les chargements de fichiers à l’aide du portail Azure][lnk-configure-upload].

## <a name="upload-a-file-from-a-simulated-device-app"></a>Téléchargement d’un fichier à partir d’une application d’appareil simulé
Dans cette section, vous allez modifier l’application d’appareil simulé que vous avez créée dans [Envoi de messages cloud-à-appareil avec IoT Hub] pour recevoir des messages cloud-à-appareil en provenance du concentrateur IoT.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **SimulatedDevice**, cliquez sur **Ajouter**, puis sur **Élément existant**. Accédez à un fichier image et ajoutez-le à votre projet. Ce didacticiel suppose que l’image a pour nom `image.jpg`.
2. Cliquez avec le bouton droit sur l’image, puis cliquez sur **Propriétés**. Assurez-vous que l’option **Copier dans le répertoire de sortie** est définie sur **Toujours copier**.
   
    ![][1]
3. Au début du fichier **Program.cs** , ajoutez les instructions suivantes :
   
        using System.IO;
4. Ajoutez la méthode suivante à la classe **Program** :
   
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();
   
            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }
   
            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }
   
    La méthode `UploadToBlobAsync` utilise le nom de fichier et la source de flux du fichier à télécharger et gère le téléchargement vers le stockage. L’application console affiche le temps nécessaire pour télécharger le fichier.
5. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :
   
        SendToBlobAsync();

> [!NOTE]
> Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling](Gestion des erreurs temporaires).
> 
> 

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier
Dans cette section, vous allez écrire une application console Windows qui reçoit des messages de notification de téléchargement de fichier à partir d’IoT Hub.

1. Dans la solution Visual Studio actuelle, créez un projet Windows Visual C# à l’aide du modèle de projet **d’application de console** . Nommez le projet **ReadFileUploadNotification**.
   
    ![Nouveau projet dans Visual Studio][2]
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadFileUploadNotification**, puis cliquez sur **Gérer les packages NuGet**.
   
    Cette action a pour effet d’afficher la fenêtre Gérer les packages NuGet.
3. Recherchez `Microsoft.Azure.Devices`, cliquez sur **Installer**et acceptez les conditions d'utilisation. 
   
    Cette action a pour effet de télécharger, d’installer et d’ajouter une référence au [package Azure IoT - Service SDK NuGet] dans le projet **ReadFileUploadNotification**.
4. Au début du fichier **Program.cs** , ajoutez les instructions suivantes :
   
        using Microsoft.Azure.Devices;
5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion du concentrateur IoT de la section [Prise en main d’IoT Hub] :
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Ajoutez la méthode suivante à la classe **Program** :
   
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
7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour **ReadFileUploadNotification** et **SimulatedDevice**.
2. Appuyez sur **F5**. Les deux applications doivent démarrer. Vous devriez voir le téléchargement terminé dans une application console et le message de notification de téléchargement reçus par l’autre application console. Vous pouvez utiliser le [portail Azure] ou l’Explorateur de serveurs Visual Studio pour vérifier la présence du fichier chargé dans votre compte Azure Storage.
   
   ![][50]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à tirer parti des fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programme][lnk-create-hub]
* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) de passerelle IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[portail Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Envoi de messages cloud-à-appareil avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Traiter les messages appareil-à-cloud]: iot-hub-csharp-csharp-process-d2c.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[package Azure IoT - Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md





<!--HONumber=Nov16_HO5-->


