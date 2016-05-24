<properties
	pageTitle="Prise en main d’Azure IoT Hub pour C# | Microsoft Azure"
	description="Didacticiel de prise en main d’Azure IoT Hub avec C#. Utilisez Azure IoT Hub et C# avec les kits de développement logiciel (SDK) de Microsoft Azure IoT pour mettre en œuvre une solution Internet des objets."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Prise en main d’Azure IoT Hub pour .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduction

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT (Internet des objets) et un serveur principal de solution. L’une des plus grandes difficultés des projets IoT consiste à connecter des appareils au serveur principal de la solution de manière fiable et sécurisée. Pour relever ce défi, IoT Hub :

- Offre une messagerie évolutive Appareil vers cloud et Cloud vers appareil fiable.
- Assure la sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès.
- Inclut des bibliothèques d’appareils pour les langages et les plateformes les plus courants.

Ce didacticiel vous explique les procédures suivantes :

- Utilisez le portail Azure pour créer un hub IoT.
- Créer une identité de l’appareil dans votre hub IoT.
- Créer un périphérique simulé qui envoie des données de télémétrie vers votre serveur principal de cloud et reçoit des commandes de ce dernier.

À la fin de ce didacticiel, vous disposerez de trois applications de console Windows :

* **CreateDeviceIdentity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre appareil simulé ;
* **ReadDeviceToCloudMessages**, qui affiche les données de télémétrie envoyées par votre périphérique simulé ;
* **SimulatedDevice**, qui se connecte à votre IoT hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole AMQPS.

> [AZURE.NOTE] L’article [kit de développement logiciel IoT Hub][lnk-hub-sdks] fournit des informations sur les différents kits de développement logiciels que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

+ Microsoft Visual Studio 2015.

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Votre hub IoT est maintenant créé et vous connaissez le nom d’hôte et la chaîne de connexion dont vous avez besoin pour terminer ce qu’il reste du didacticiel.

## Création d’une identité d’appareil

Dans cette section, vous allez créer une application console Windows qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **CreateDeviceIdentity**.

	![][10]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **CreateDeviceIdentity**, puis cliquez sur **Gérer les packages NuGet**.

3. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation.

	![][11]

4. Cette opération lance le téléchargement, l’installation et ajoute une référence au package NuGet [kit de développement logiciel (SDK) de service Microsoft Azure IoT][lnk-nuget-service-sdk] et ses dépendances.

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section précédente :

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

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

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Exécutez cette application et notez la clé de l’appareil.

    ![][12]

> [AZURE.NOTE] Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité. Par ailleurs, un indicateur activé/désactivé vous permet de désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.

## Recevoir des messages appareil-à-cloud

Dans cette section, vous allez créer une application console Windows qui lit les messages appareil-à-cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traitement de messages de type « Appareil vers cloud »][lnk-process-d2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [AZURE.NOTE] Le point de terminaison compatible Event Hubs pour lire des messages de l'appareil vers le cloud utilise toujours le protocole AMQPS.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **ReadDeviceToCloudMessages**.

    ![][10]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadDeviceToCloudMessages**, puis cliquez sur **Gérer les packages NuGet**.

3. Dans la fenêtre **Gestionnaire de package NuGet**, recherchez **WindowsAzure.ServiceBus**, cliquez sur **Installer** et acceptez les conditions d’utilisation.

    Cette opération lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus][lnk-servicebus-nuget] avec toutes ses dépendances. Ce package permet à l'application de se connecter au point de terminaison compatible Event Hubs sur votre IoT hub.

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Ajoutez les champs suivants à la classe **Program**, en remplaçant la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section *Créer un IoT Hub* :

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Ajoutez la méthode suivante à la classe **Program** :

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

    Cette méthode utilise une instance **EventHubReceiver** pour recevoir des messages à partir de toutes les partitions de réception Appareil vers cloud IoT Hub. Notez la manière dont vous transmettez un paramètre `DateTime.Now` lorsque vous créez l’objet **EventHubReceiver** pour qu’il reçoive uniquement les messages envoyés après son démarrage. Cette opération est utile dans un environnement de test, car elle vous permet de voir l’ensemble actuel de messages, mais dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, voir le didacticiel [Traiter les messages appareil-à-cloud IoT Hub][lnk-process-d2c-tutorial].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

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

## Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application console Windows qui simule un appareil envoyant des messages Appareil vers cloud à un IoT Hub.

1. Dans Visual Studio, ajoutez un nouveau projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **SimulatedDevice**.

   	![][10]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulatedDevice**, puis cliquez sur **Gérer les packages NuGet**.

3. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **Microsoft.Azure.Devices.Client**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation.

	Cette opération lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT - Device SDK NuGet][lnk-device-nuget] et ses dépendances.

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;


5. Ajoutez les champs suivants à la classe **Program**, en remplaçant les valeurs des espaces réservés par le nom d’hôte IoT Hub figurant dans la section *Créer un IoT Hub* et par la clé d’appareil figurant dans la section *Créer une identité d’appareil* :

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

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

                Task.Delay(1000).Wait();
            }
        }

	Cette méthode envoie un nouveau message Appareil vers cloud toutes les secondes. Le message contient un objet JSON sérialisé avec l’ID de l’appareil et un nombre généré de manière aléatoire pour simuler un anémomètre.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Par défaut, la méthode de création **Create** crée une instance **DeviceClient** qui utilise le protocole AMQP pour communiquer avec IoT Hub. Pour utiliser le protocole HTTPS, remplacez la méthode **Create** afin de pouvoir spécifier le protocole. Si vous choisissez d’utiliser le protocole HTTPS, vous devez également ajouter le package NuGet **Microsoft.AspNet.WebApi.Client** à votre projet de manière à inclure l’espace de noms **System.Net.Http.Formatting**.

Ce didacticiel vous accompagne tout au long des étapes de création d'un client d'appareil IoT Hub. Vous pouvez également utiliser l’extension [Connected Service for Azure IoT Hub][lnk-connected-service] de Visual Studio pour ajouter le code nécessaire à votre application de client d’appareil.


> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Dans Visual Studio, dans l’explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sur **Définir comme projet de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez **Démarrer** en tant qu’**action** pour les projets **ReadDeviceToCloudMessages** et **SimulatedDevice**.

   	![][41]

2.	Appuyez sur **F5** pour démarrer les deux applications. La sortie de console de l’application **SimulatedDevice** affiche les messages que votre périphérique simulé envoie à votre concentrateur IoT et la sortie de console de l’application **ReadDeviceToCloudMessages** affiche les messages que votre IoT hub reçoit.

   	![][42]

3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au concentrateur :

    ![][43]


## Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau concentrateur IoT dans le portail, puis créé une identité d’appareil dans le registre d’identités du concentrateur. Vous avez utilisé cette identité d’appareil pour activer l'appareil simulé qui envoie des messages de l’appareil vers le cloud au concentrateur et créé une application qui affiche les messages reçus par le concentrateur. Vous pouvez continuer à explorer les fonctionnalités d’IoT Hub et d’autres scénarios IoT dans les didacticiels suivants :

- [Envoyer des messages du cloud vers des appareils avec IoT Hub][lnk-c2d-tutorial] montre comment envoyer des messages aux appareils et traiter les informations de remise générées par IoT Hub.
- [Traiter les messages des appareils vers le cloud][lnk-process-d2c-tutorial] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.
- [Téléchargement de fichiers à partir d’appareils][lnk-upload-tutorial], qui décrit un modèle qui utilise les messages du cloud vers les appareils pour faciliter les téléchargements de fichiers à partir d’appareils.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!---HONumber=AcomDC_0511_2016-->