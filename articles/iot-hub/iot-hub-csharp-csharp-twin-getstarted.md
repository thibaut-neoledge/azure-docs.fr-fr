---
title: "Bien démarrer avec les jumeaux d’appareil Azure IoT Hub (.NET/.NET) | Microsoft Docs"
description: "Guide d’utilisation des jumeaux d’appareils Azure IoT Hub pour ajouter des balises, puis utiliser une requête IoT Hub. Vous utilisez le kit Azure IoT device SDK pour .NET pour implémenter l’application d’appareil simulé et le kit Azure IoT service SDK pour .NET pour implémenter une application de service qui ajoute des balises et exécute la requête IoT Hub."
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 6073d594117e69676b753a1e3af25fffa3583a2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-netnet"></a>Bien démarrer avec les jumeaux d’appareils (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

À la fin de ce didacticiel, vous disposerez des deux applications console .NET suivantes :

* **CreateDeviceIdentity**, application .NET qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre application d’appareil simulé.
* **AddTagsAndQuery**, application principale .NET qui ajoute des balises et interroge des jumeaux d’appareil.
* **ReportConnectivity**, application pour appareil .NET qui simule un appareil se connectant à votre hub IoT avec l’identité d’appareil créée précédemment, et signale son état de connectivité.

> [!NOTE]
> L’article [Kits Azure IoT SDK][lnk-hub-sdks] fournit des informations sur les kits Azure IoT SDK que vous pouvez utiliser pour générer des applications pour appareil et des applications principales.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Si vous voulez plutôt créer l’identité de l’appareil par programmation, lisez la section correspondante dans l’article [Connexion de l’appareil simulé à votre hub IoT à l’aide de .NET] [lnk-device-identity-csharp].

## <a name="create-the-service-app"></a>Créer l’application de service
Dans cette section, vous créez une application console (utilisant C#) qui ajoute des métadonnées d’emplacement au jumeau d’appareil associé à **myDeviceId**. L’application console interroge ensuite les jumeaux d’appareil stockés dans le hub IoT en sélectionnant les appareils situés aux États-Unis, puis ceux qui signalent une connexion mobile.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **AddTagsAndQuery**.
   
    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **AddTagsAndQuery**, puis cliquez sur **Gérer les packages NuGet...**.
1. Dans la fenêtre **Gestionnaire de Package NuGet**, sélectionnez **Parcourir**, puis recherchez **microsoft.azure.devices**. Sélectionnez **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.
   
    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]
1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section précédente.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Ajoutez la méthode suivante à la classe **Program** :
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    La classe **RegistryManager** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir du service. Le code précédent initialise l’objet **registryManager**, récupère le jumeau d’appareil de **myDeviceId**, puis met à jour ses balises avec les informations d’emplacement souhaitées.
   
    Après la mise à jour, il exécute deux requêtes : la première sélectionne uniquement les jumeaux d’appareils situés dans l’usine **Redmond43** et la seconde affine la requête pour sélectionner uniquement les appareils qui sont également connectés via un réseau cellulaire.
   
    Notez que le code précédent, quand il crée l’objet **query**, spécifie un nombre maximal de documents retournés. L’objet **query** contient une propriété booléenne **HasMoreResults** permettant d’appeler les méthodes **GetNextAsTwinAsync** plusieurs fois afin de récupérer tous les résultats. Une méthode appelée **GetNextAsJson** est disponible pour les résultats qui ne sont pas des jumeaux d’appareil, par exemple, les résultats de requêtes d’agrégation.
1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. Dans l’Explorateur de solutions, sélectionnez **Définir les projets de démarrage...** et vérifiez que l’**Action** définie pour le projet **AddTagsAndQuery** est **Démarrer**. Générez la solution.
1. Exécutez cette application en cliquant avec le bouton droit sur le projet **AddTagsAndQuery** et en sélectionnant **Déboguer**, puis **Démarrer une nouvelle instance**. Vous devriez voir un appareil dans les résultats de la requête demandant tous les appareils situés à **Redmond43**, et aucun pour la requête limitant les résultats aux appareils utilisant un réseau cellulaire.
   
    ![Résultats de requête dans la fenêtre][img-addtagapp]

Dans la section suivante, vous allez créer une application d’appareil qui transmet les informations de connectivité et modifie le résultat de la requête de la section précédente.

## <a name="create-the-device-app"></a>Créer l’application pour appareil
Dans cette section, vous allez créer une application console .NET qui se connecte à votre hub en tant que **myDeviceId**, puis met à jour ses propriétés signalées afin qu’elles contiennent les informations indiquant qu’elle est connectée par le biais d’un réseau mobile.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **ReportConnectivity**.
   
    ![Nouvelle application pour appareil Windows classique Visual C#][img-createdeviceapp]
    
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReportConnectivity**, puis cliquez sur **Gérer les packages NuGet...**
1. Dans la fenêtre **Gestionnaire de package NuGet**, sélectionnez **Parcourir**, puis recherchez **microsoft.azure.devices.client**. Sélectionnez **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence au package NuGet [ Azure IoT device SDK][lnk-nuget-client-sdk] et à ses dépendances.
   
    ![Application cliente dans la fenêtre du gestionnaire de package NuGet][img-clientnuget]
1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion de l’appareil notée dans la section précédente.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Ajoutez la méthode suivante à la classe **Program** :

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    L’objet **Client** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir de l’appareil. Le code présenté ci-dessus initialise l’objet **Client**, puis récupère le jumeau d’appareil pour **myDeviceId**.

1. Ajoutez la méthode suivante à la classe **Program** :
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   Le code ci-dessus met à jour la propriété signalée de **myDeviceId** avec les informations de connectivité.

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. Dans l’Explorateur de solutions, ouvrez **Définir les projets de démarrage...** et vérifiez que la valeur **Action** définie pour le projet **ReportConnectivity** est **Démarrer**. Générez la solution.
1. Exécutez cette application en cliquant avec le bouton droit sur le projet **ReportConnectivity** et en sélectionnant **Déboguer**, puis **Démarrer une nouvelle instance**. Vous devez la voir obtenir les informations relatives au jumeau et envoyer la connectivité sous la forme d’une *propriété signalée*.
   
    ![Exécuter l’application pour appareil pour signaler la connectivité][img-rundeviceapp]
    
    
1. À présent que l’appareil a signalé ses informations de connectivité, il doit apparaître dans les deux requêtes. Exécutez l’application .NET **AddTagsAndQuery** pour exécuter des requêtes à nouveau. Cette fois, **myDeviceId** doit apparaître dans les résultats des deux requêtes.
   
    ![Connectivité des appareils signalée avec succès][img-tagappsuccess]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau hub IoT dans le portail Azure, puis créé une identité d’appareil dans le registre des identités du hub IoT. Vous avez ajouté des métadonnées d’appareil en tant que balises à partir d’une application principale et écrit une application pour appareil simulée pour signaler des informations de connectivité d’appareil dans le jumeau d’appareil. Vous avez également appris à interroger ces informations à l’aide du langage de requête IoT Hub de type SQL.

Utilisez les ressources suivantes :

* Pour savoir comment envoyer les données de télémétrie à partir d’appareils, consultez le didacticiel [Prise en main d’IoT Hub][lnk-iothub-getstarted].
* Pour savoir comment configurer des appareils à l’aide des propriétés de jumeau d’appareil souhaitées, voir le didacticiel [Utiliser des propriétés souhaitées pour configurer des appareils][lnk-twin-how-to-configure].
* Pour savoir comment contrôler des appareils de façon interactive (par exemple en mettant en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), voir le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

