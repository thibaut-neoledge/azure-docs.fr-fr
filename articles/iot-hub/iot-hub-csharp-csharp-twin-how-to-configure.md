---
title: "Utilisation des propriétés de jumeau d’appareil Azure IoT Hub (.NET/.NET) | Microsoft Docs"
description: "Guide d’utilisation des représentations d’appareils Azure IoT Hub pour configurer des appareils. Vous utilisez Azure IoT device SDK pour .NET afin d’implémenter une application d’appareil simulé et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui modifie une configuration d’appareil avec un jumeau d’appareil."
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 679cda28bf3ce9fb207fe3693a3453b355f1de15
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---
# <a name="use-desired-properties-to-configure-devices"></a>Utilisation des propriétés souhaitées pour configurer des appareils
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous disposerez de deux applications de console .NET :

* **SimulateDeviceConfiguration**, application d’appareil simulée qui attend une mise à jour de configuration souhaitée, et signale l’état d’un processus de mise à jour de configuration simulée.
* **SetDesiredConfigurationAndQuery**, application sur le serveur principal qui définit la configuration souhaitée sur un appareil et interroge le processus de mise à jour de configuration.

> [!NOTE]
> L’article [Kits Azure IoT SDK][lnk-hub-sdks] fournit des informations sur les kits Azure IoT SDK que vous pouvez utiliser pour générer des applications pour appareil et des applications principales.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.

Si vous avez suivi le didacticiel [Prise en main des jumeaux d’appareils][lnk-twin-tutorial], vous avez déjà un IoT Hub et une identité d’appareil nommée **myDeviceId**. Vous pouvez donc passer à la section [Créer l’application d’appareil simulé][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Créer l’application d’appareil simulée
Dans cette section, vous créez une application de console .NET qui se connecte à votre hub en tant que **myDeviceId**, attend une mise à jour de la configuration souhaitée, puis signale des mises à jour sur le processus de mise à jour de configuration simulée.

1. Dans Visual Studio, créez un projet Visual C# Bureau classique Windows en utilisant le modèle de projet **Application de console**. Nommez le projet **SimulateDeviceConfiguration**.
   
    ![Nouvelle application pour appareil Windows classique Visual C#][img-createdeviceapp]

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulateDeviceConfiguration**, puis cliquez sur **Gérer les packages NuGet**.
1. Dans la fenêtre **Gestionnaire de package NuGet**, sélectionnez **Parcourir**, puis recherchez **microsoft.azure.devices.client**. Sélectionnez **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence au package NuGet [ Azure IoT device SDK][lnk-nuget-client-sdk] et à ses dépendances.
   
    ![Application cliente dans la fenêtre du gestionnaire de package NuGet][img-clientnuget]
1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion de l’appareil notée dans la section précédente.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. Ajoutez la méthode suivante à la classe **Program** :
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    L’objet **Client** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir de l’appareil. Le code présenté ci-dessus initialise l’objet **Client**, puis récupère le jumeau d’appareil pour **myDeviceId**.

1. Ajoutez la méthode suivante à la classe **Program**. Cette méthode définit les valeurs initiales de télémétrie sur l’appareil local et met à jour le jumeau d’appareil.

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Ajoutez la méthode suivante à la classe **Program**. Il s’agit d’un rappel qui détecte une modification des *propriétés souhaitée* dans le jumeau d’appareil.

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Cette méthode met à jour les propriétés signalées sur l’objet de jumeau d’appareil local avec la requête de mise à jour de configuration, et définit l’état sur **En attente**, puis met à jour le jumeau d’appareil sur le service. Après la mise à jour du jumeau d’appareil, la modification de la configuration se termine par l’appel à la méthode `CompleteConfigChange` décrite dans le point suivant.

1. Ajoutez la méthode suivante à la classe **Program**. Cette méthode simule une réinitialisation de l’appareil, puis met à jour les propriétés locales signalées en définissant l’état sur **Réussite**, et supprime l’élément **pendingConfig**. Ensuite, elle met à jour la représentation d’appareils sur le service. 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallback(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > Ce didacticiel ne simule pas de comportement pour des mises à jour de configuration simultanées. Certains processus de mise à jour de configuration peuvent s’adapter à des modifications de configuration de la cible en cours de mise à jour, d’autres peuvent avoir à les mettre en file d’attente, et d’autres encore peuvent les refuser avec une condition d’erreur. Prenez en considération le comportement souhaité pour votre processus de configuration spécifique, et ajoutez la logique appropriée avant de lancer la modification de la configuration.
   > 
   > 
1. Générez la solution, puis exécutez l’application d’appareil à partir de Visual Studio en cliquant sur **F5**. Dans la console de sortie, vous devez voir les messages indiquant que votre appareil simulé récupère le jumeau d’appareil, configure la télémétrie et attend la modification de la propriété souhaitée. Gardez l’application active.

## <a name="create-the-service-app"></a>Créer l’application de service
Dans cette section, vous créez une application console .NET qui met à jour les *propriétés souhaitées* sur la représentation d’appareils associée à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Elle interroge ensuite les représentations d’appareils stockées dans l’IoT Hub, puis affiche la différence entre les configurations souhaitées et signalées de l’appareil.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SetDesiredConfigurationAndQuery**.
   
    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SetDesiredConfigurationAndQuery**, puis cliquez sur **Gérer les packages NuGet...**.
1. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.
   
    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]
1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section précédente.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Ajoutez la méthode suivante à la classe **Program** :
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des représentations d’appareil à partir du service. Ce code initialise l’objet **Registry**, récupère le jumeau d’appareils de **myDeviceId**, puis met à jour ses propriétés souhaitées avec un nouvel objet de configuration de télémétrie.
    Après cela, il interroge toutes les 10 secondes les jumeaux d’appareils stockées dans l’IoT Hub, puis imprime les configurations de télémétrie souhaitées et signalées. Pour savoir comment générer des rapports riches sur tous vos appareils, voir le [langage de requête IoT Hub][lnk-query].
   
   > [!IMPORTANT]
   > Cette application interroge IoT Hub toutes les 10 secondes à titre d’exemple. Utilisez des requêtes pour générer des rapports côté utilisateur sur plusieurs appareils, non pour détecter des modifications. Si votre solution nécessite des notifications d’événements d’appareil en temps réel, utilisez des [notifications jumelles][lnk-twin-notifications].
   > 
   > 
1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. Dans l’Explorateur de solutions, sélectionnez **Définir les projets de démarrage...** et vérifiez que l’**Action** définie pour le projet **SetDesiredConfigurationAndQuery** est **Démarrer**. Générez la solution.
1. Alors que l’application d’appareil **SimulateDeviceConfiguration** est en cours d’exécution, exécutez l’application de service à partir de Visual Studio en utilisant **F5**. Vous devriez voir la configuration signalée passer de **En attente** à **Réussite**, avec la nouvelle fréquence d’envoi active de 5 minutes au lieu de 24 heures.

 ![Appareil configuré correctement][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Un délai pouvant atteindre une minute s’écoule entre l’opération de signalement de l’appareil et le résultat de la requête. Il permet à l’infrastructure de requête d’opérer à très grande échelle. Pour récupérer des vues cohérentes d’une représentation d’appareil, utilisez la méthode **getDeviceTwin** dans la classe **Registry**.
   > 
   > 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez défini une configuration souhaitée en tant que *propriétés souhaitées* à partir du serveur principal et écrit une application d’appareil pour détecter cette modification et simuler un processus de mise à jour en plusieurs étapes signalant son état par le biais des propriétés signalées.

Utilisez les ressources suivantes :

* Pour savoir comment envoyer les données de télémétrie à partir d’appareils, consultez le didacticiel [Prise en main d’IoT Hub][lnk-iothub-getstarted].
* Pour savoir comment planifier ou exécuter des opérations sur de grands ensembles d’appareils, consultez le didacticiel [Planifier et diffuser des travaux][lnk-schedule-jobs].
* Pour savoir comment contrôler des appareils de façon interactive (par exemple en mettant en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), consultez le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app

