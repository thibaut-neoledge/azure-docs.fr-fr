---
title: "Créer un module Azure IoT Edge avec C# | Microsoft Docs"
description: "Ce didacticiel explique comment écrire un module de convertisseur de données BLE en utilisant les derniers packages NuGet Azure IoT Edge, Visual Studio Code et C#."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: azure, iot, didacticiel, module, nuget, vscode, csharp, edge
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 91ff3c96e4d7928131eba83c0e8c1951782447be
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Créer un module Azure IoT Edge avec C&#x23;

Ce didacticiel explique comment créer un module pour `Azure IoT Edge` à l’aide de `Visual Studio Code` et `C#`.

Dans ce didacticiel, nous aborderons la configuration de l’environnement et expliquerons comment écrire un module de convertisseur de données [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) en utilisant les derniers packages `Azure IoT Edge NuGet`. 

>[!NOTE]
Ce didacticiel utilise le `.NET Core SDK`, qui prend en charge une compatibilité multiplateforme. Le didacticiel suivant est écrit à l’aide du système d’exploitation `Windows 10`. Certaines commandes de ce didacticiel peuvent être différentes selon votre `development environment`. 

## <a name="prerequisites"></a>Composants requis

Dans cette section, vous configurez votre environnement pour le développement du module `Azure IoT Edge`. Cela s’applique aux systèmes d’exploitation **Windows 64 bits** et **Linux 64 bits (Ubuntu/Debian 8)**.

Les logiciels suivants sont requis :

- [Client Git](https://git-scm.com/downloads).
- [Kit de développement logiciel (SDK) principal NET](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Vous n’avez pas besoin de cloner le référentiel pour cet exemple. Toutefois, tous les exemples de code présentés dans ce didacticiel se trouvent dans le référentiel suivant :

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Prise en main

1. Installez `.NET Core SDK`.
2. Installez `Visual Studio Code` et `C# extension` à partir de la Place de marché Visual Studio Code.

Visionnez ce [bref didacticiel vidéo](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) sur la prise en main de `Visual Studio Code` et `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Création du module de convertisseur Azure IoT Edge

1. Initialisez un nouveau projet C# de bibliothèque de classes `.NET Core` :
    - Ouvrez une invite de commandes (`Windows + R` -> `cmd` -> `enter`).
    - Accédez au dossier dans lequel vous souhaitez créer le projet `C#`.
    - Tapez **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Cette commande crée une classe vide appelée `Class1.cs` dans votre répertoire de projets.
2. Accédez au dossier dans lequel nous venons de créer le projet de bibliothèque de classes en tapant **cd IoTEdgeConverterModule**.
3. Ouvrez le projet dans `Visual Studio Code` en tapant **code**.
4. Une fois le projet ouvert dans `Visual Studio Code`, cliquez sur **IoTEdgeConverterModule.csproj** pour ouvrir le fichier, comme illustré dans l’image suivante :

    ![Fenêtre de modification - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Insérez le blob `XML` indiqué dans l’extrait de code suivant entre la balise de fin `PropertyGroup` et la balise de fin `Project` (ligne six dans l’image précédente) et enregistrez le fichier en appuyant sur `Ctrl` + `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. Une fois que vous enregistrez le fichier `.csproj`, `Visual Studio Code` vous invite avec une boîte de dialogue `unresolved dependencies` comme illustré dans l’image suivante : 

    ![Boîte de dialogue pour restaurer des dépendances de restauration - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) Cliquez sur `Restore` pour restaurer toutes les références dans le fichier `.csproj` des projets, y compris l’élément `PackageReferences` que nous avons ajouté. 

    (b) `Visual Studio Code` crée automatiquement le fichier `project.assets.json` dans le dossier `obj` de vos projets. Ce fichier contient des informations sur les dépendances de votre projet pour accélérer les restaurations suivantes.
 
    >[!NOTE]
    `.NET Core Tools` sont désormais basés sur MSBuild. Ce qui signifie qu’un fichier de projet `.csproj` est créé à la place d’un `project.json`.

    - Si `Visual Studio Code` n’affiche aucune invite vous indiquant que tout fonctionne normalement, nous pouvons le faire manuellement. Ouvrez la fenêtre de terminal intégré `Visual Studio Code` en appuyant sur les touches `Ctrl` + `backtick` ou utilisant les menus `View` -> `Integrated Terminal`.
    - Dans la fenêtre `Integrated Terminal`, tapez **dotnet restore**.
    
7. Renommez le fichier `Class1.cs` par `BleConverterModule.cs`. 

    a) Pour renommer le fichier, commencez par cliquer dessus, puis appuyez sur la touche `F2`.
    
    b) Tapez le nouveau nom **BleConverterModule** comme illustré dans l’image suivante :

    ![Renommer une classe - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Remplacez le code existant dans le fichier `BleConverterModule.cs` en copiant-collant l’extrait de code suivant dans votre fichier `BleConverterModule.cs`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Enregistrez le fichier en appuyant sur `Ctrl` + `S`.

10. Créez un fichier appelé `Untitled-1` en appuyant sur les touches `Ctrl` + `N` comme illustré dans l’image suivante :

    ![Nouveau fichier - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Pour désérialiser l’objet `JSON` que nous recevons de l’appareil `BLE` simulé, copiez le code suivant dans la fenêtre d’éditeur de code du fichier `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Enregistrez le fichier sous `BleData.cs` en appuyant sur les touches `Ctrl` + `Shift` + `S`.
    - Dans la boîte de dialogue Enregistrer sous, dans le menu déroulant `Save as Type`, sélectionnez `C# (*.cs;*.csx)` comme illustré dans l’image suivante :

    ![Boîte de dialogue pour enregistrer sous - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Créez un fichier appelé `Untitled-1` en appuyant sur les touches `Ctrl` + `N`.

14. Copiez et collez l’extrait de code suivant dans le fichier `Untitled-1`. Cette classe est un module `Azure IoT Edge`, qui nous permet de générer les données reçues à partir de notre `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Enregistrez le fichier sous `DotNetPrinterModule.cs` en appuyant sur `Ctrl` + `Shift` + `S`.
    - Dans la boîte de dialogue Enregistrer sous, dans le menu déroulant `Save as Type`, sélectionnez `C# (*.cs;*.csx)`.

16. Créez un fichier appelé `Untitled-1` en appuyant sur les touches `Ctrl` + `N`.

17. Pour désérialiser l’objet `JSON` que nous recevons de `BleConverterModule`, copiez et collez l’extrait de code suivant dans le fichier `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Enregistrez le fichier sous `BleConverterData.cs` en appuyant sur `Ctrl` + `Shift` + `S`.
    - Dans la boîte de dialogue Enregistrer sous, dans le menu déroulant `Save as Type`, sélectionnez `C# (*.cs;*.csx)`.

19. Créez un fichier appelé `Untitled-1` en appuyant sur les touches `Ctrl` + `N`.

20. Copiez et collez l’extrait de code suivant dans le fichier `Untitled-1`.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Enregistrez le fichier sous `gw-config.json` en appuyant sur `Ctrl` + `Shift` + `S`.
    - Dans la boîte de dialogue Enregistrer sous, dans le menu déroulant `Save as Type`, sélectionnez `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Pour activer la copie du fichier config dans le répertoire de sortie, mettez à jour `IoTEdgeConverterModule.csproj` avec le blob XML suivant :

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - L’élément `IoTEdgeConverterModule.csproj` mis à jour doit ressembler à l’image suivante :

    ![Fichier .csproj mis à jour - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Créez un fichier appelé `Untitled-1` en appuyant sur les touches `Ctrl` + `N`.

24. Copiez et collez l’extrait de code suivant dans le fichier `Untitled-1`.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Enregistrez le fichier sous `binplace.ps1` en appuyant sur `Ctrl` + `Shift` + `S`.
    - Dans la boîte de dialogue Enregistrer sous, dans le menu déroulant `Save as Type`, sélectionnez `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Générez le projet en appuyant sur les touches `Ctrl` + `Shift` + `B`. Lorsque vous générez le projet pour la première fois, `Visual Studio Code` vous invite via la boîte de dialogue `No build task defined.` comme illustré dans l’image suivante :

    ![Boîte de dialogue de tâche de génération - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) Cliquez sur le bouton `Configure Build Task`.

    b) Dans le menu déroulant de la boîte de dialogue `Select a Task Runner`, sélectionnez `.NET Core` comme illustré dans l’image suivante : 

    ![Boîte de dialogue pour sélectionner une tâche - Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) Cliquer sur l’élément `.NET Core` crée le fichier `tasks.json` dans votre répertoire `.vscode` et ouvre le fichier dans la fenêtre `code editor`. Il n’est pas nécessaire de modifier ce fichier, vous pouvez donc fermer l’onglet.

27.  Ouvrez la fenêtre de terminal intégré `Visual Studio Code` en appuyant sur les touches `Ctrl` + `backtick` ou en utilisant les menus `View` -> `Integrated Terminal`, puis tapez **.\binplace.ps1** dans l’invite de commandes `PowerShell`. Cette commande copie toutes nos dépendances dans le répertoire de sortie.

28. Accédez au répertoire de sortie des projets dans la fenêtre `Integrated Terminal` en tapant **cd .\bin\Debug\netstandard1.3**.

29. Exécutez l’exemple de projet en tapant **.\gw.exe gw-config.json** dans l’invite de la fenêtre `Integrated Terminal`. 
    - Si vous avez soigneusement suivi les étapes de ce didacticiel, l’exemple de projet `Azure IoT Edge BLE Data Converter Module` doit être en cours d’exécution comme illustré dans l’image suivante :
    
        ![Exemple d’appareil simulé en cours d’exécution dans Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Si vous souhaitez arrêter l’application, appuyez sur la touche `<Enter>`.

>[!IMPORTANT]
Il est déconseillé d’utiliser `Ctrl` + `C` pour arrêter l’application de passerelle `IoT Edge` (c’est-à-dire **gw.exe**). Cela peut entraîner l’arrêt anormal du processus.


