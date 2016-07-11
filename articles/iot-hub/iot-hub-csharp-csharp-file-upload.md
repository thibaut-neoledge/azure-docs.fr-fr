<properties
	pageTitle="Télécharger des fichiers à partir d’appareils à l’aide d’IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour découvrir comment télécharger des fichiers à partir d’appareils à l’aide d’Azure IoT Hub avec C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Didacticiel : télécharger des fichiers à partir d’appareils vers le cloud avec IoT Hub

## Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Les didacticiels précédents ([Prise en main d’IoT Hub] et [Envoi de messages cloud à appareil avec IoT Hub]) illustrent la fonctionnalité de base des messages appareil à cloud et cloud à appareil offerte par IoT Hub, et le didacticiel [Traitement des messages appareil à cloud] explique comment stocker de façon fiable les messages appareil à cloud dans le stockage d’objets blob Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Il s’agit par exemple de fichiers volumineux contenant des images, des vidéos, des exemples de données de vibration à haute fréquence ou contenant une forme quelconque de données prétraitées. Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory] ou de la pile [Hadoop]. Quand le téléchargement d’un fichier à partir d’un appareil est préférable à l’envoi d’événements, vous pouvez néanmoins utiliser les fonctionnalités de fiabilité et de sécurité offertes par IoT Hub.

Ce didacticiel s’appuie sur le code du didacticiel [Envoi de messages cloud à appareil avec IoT Hub] pour vous montrer comment utiliser les fonctions de téléchargement de fichier d’IoT Hub. Il vous explique comment fournir de manière sécurisée un URI d’objet blob Azure à un appareil pour télécharger un fichier et comment utiliser les notifications de téléchargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur d’applications principal.

À la fin de ce didacticiel, vous exécuterez deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans le didacticiel [Envoi de messages cloud à appareil avec IoT Hub], qui télécharge un fichier dans le stockage à l’aide d’un URI SAS fourni par votre IoT Hub.
* **ReadFileUploadNotification**, qui reçoit les notifications de téléchargement de fichier à partir de votre IoT Hub.

> [AZURE.NOTE] IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions étape par étape expliquant comment connecter votre appareil au code affiché dans ce didacticiel, et généralement à Azure IoT Hub, consultez le [Centre de développement Azure IoT].

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit d’Azure][lnk-free-trial].

## Associer un compte Azure Storage à IoT Hub

Étant donné que l’appareil simulé charge un fichier dans un objet blob Azure Storage, vous devez disposer d’un compte [Azure Storage] associé à IoT Hub. Lorsque vous associez un compte de stockage à un concentrateur IoT, le concentrateur peut générer un URI SAS qu’un appareil peut utiliser pour charger en toute sécurité un fichier vers un conteneur d’objets blob. Le service IoT Hub et les Kits de développement logiciel (SDK) d’appareil coordonnent le processus qui génère l’URI SAS et le rend disponible pour un appareil à utiliser pour charger un fichier.

Vous pouvez associer un compte Azure Storage à votre concentrateur IoT en suivant les instructions de la section [Configuration de hubs IoT par le biais du portail Azure].

## Téléchargement d’un fichier à partir d’un appareil simulé

Dans cette section, vous allez modifier l’application de l’appareil simulé que vous avez créée dans [Didacticiel : envoi de messages cloud-à-appareil avec IoT Hub] pour recevoir des messages cloud-à-appareil à partir du concentrateur IoT.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **SimulatedDevice**, cliquez sur **Ajouter**, puis sur **Élément existant**. Accédez à un fichier image et ajoutez-le à votre projet. Ce didacticiel suppose que l’image a pour nom `image.jpg`.

2. Cliquez avec le bouton droit sur l’image, puis cliquez sur **Propriétés**. Assurez-vous que l’option **Copier dans le répertoire de sortie** est définie sur **Toujours copier**.

    ![][1]

3. Au début du fichier **Program.cs**, ajoutez les instructions suivantes :

        using System.IO;

4. Ajoutez la méthode suivante à la classe **Program** :
         
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

    La méthode `UploadToBlobAsync` utilise le nom de fichier et la source de flux du fichier à charger et gère le chargement vers le stockage. L’application console affiche le temps nécessaire pour télécharger le fichier.

5. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

        SendToBlobAsync();

> [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] (Gestion des erreurs temporaires).

## Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez écrire une application console Windows qui reçoit des messages de notification de téléchargement de fichier à partir d’IoT Hub.

1. Dans la solution Visual Studio actuelle, créez un projet Windows Visual C# à l’aide du modèle de projet d’**application de console**. Nommez le projet **ReadFileUploadNotification**.

    ![Nouveau projet dans Visual Studio][2]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadFileUploadNotification**, puis cliquez sur **Gérer les packages NuGet**.

    La fenêtre Gérer les packages NuGet s’affiche.

2. Recherchez `Microsoft.Azure.Devices`, cliquez sur **Installer** et acceptez les conditions d'utilisation.

	Cette opération lance le téléchargement, l’installation et l’ajout d’une référence au [package Azure IoT - Service SDK NuGet] dans le projet **ReadFileUploadNotification**.

3. Au début du fichier **Program.cs**, ajoutez les instructions suivantes :

        using Microsoft.Azure.Devices;

4. Ajoutez les champs suivants à la classe **Program**. Remplacez la valeur d’espace réservé par la chaîne de connexion du concentrateur IoT de la section [Prise en main d’Azure IoT Hub] \:

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

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour **ReadFileUploadNotification** et **SimulatedDevice**.

2. Appuyez sur **F5**. Les deux applications doivent démarrer. Vous devriez voir le téléchargement terminé dans une application console et le message de notification de téléchargement reçus par l’autre application console. Vous pouvez utiliser le [portail Azure] ou l’Explorateur de serveurs Visual Studio pour vérifier la présence du fichier chargé dans votre compte de stockage.

  ![][50]


## Étapes suivantes

Dans ce didacticiel, vous avez appris à tirer parti des fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et les fonctionnalités d’IoT Hub avec le didacticiel suivant :

- [Traiter les messages des appareils vers le cloud] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.

Informations supplémentaires sur IoT Hub :

* [Vue d’ensemble d’IoT Hub]
* [Guide du développeur d’IoT Hub]
* [Conseils pour IoT Hub]
* [Langages et plateformes d’appareils pris en charge][Supported devices]
* [Centre de développement Azure IoT]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Envoyer des messages du cloud vers des appareils avec IoT Hub]: iot-hub-csharp-csharp-c2d.md

[portail Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Didacticiel : envoi de messages cloud-à-appareil avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Envoi de messages cloud à appareil avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Traitement des messages appareil à cloud]: iot-hub-csharp-csharp-process-d2c.md
[Traiter les messages des appareils vers le cloud]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Vue d’ensemble d’IoT Hub]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Prise en main d’Azure IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Configuration de hubs IoT par le biais du portail Azure]: iot-hub-manage-through-portal.md#file-upload
[package Azure IoT - Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0629_2016-->