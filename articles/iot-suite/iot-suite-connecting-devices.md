---
title: "Connexion d’un périphérique à l’aide de C sur Windows | Microsoft Docs"
description: "Explique comment connecter un appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite en C et exécutée sous Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af83b55210d1e31c548b9b178272f7e75be7b98e


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Création d’un exemple de solution C sur Windows
Les étapes suivantes vous montrent comment utiliser Visual Studio afin de créer une application cliente écrite en C qui communique avec la solution préconfigurée de surveillance à distance.

Créez un projet de démarrage dans Visual Studio 2015 et ajoutez les packages NuGet clients de l’appareil IoT Hub :

1. Dans Visual Studio 2015, créez une application console C à l’aide du modèle **Application console Win32** de Visual C++. Nommez le projet **RMDevice**.
2. Sur la page **Paramètres de l’application** dans **l’Assistant Application Win32**, assurez-vous que l’option **Application console** est sélectionnée et décochez les cases **En-tête précompilé** et **Vérifications SDL (Security Development Lifecycle)**.
3. Dans l’ **Explorateur de solutions**, supprimez les fichiers stdafx.h, targetver.h et stdafx.cpp.
4. Dans l’ **Explorateur de solutions**, renommez le fichier RMDevice.cpp en RMDevice.c.
5. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, puis cliquez sur **Gérer les packages NuGet**. Cliquez sur **Parcourir**, puis recherchez et installez les packages NuGet suivants dans le projet :
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.HttpTransport
6. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, puis cliquez sur **Propriétés** pour ouvrir la boîte de dialogue **Pages de propriétés** du projet. Pour plus d’informations, consultez [Setting Visual C++ Project Properties (Définition des propriétés de projet Visual C++)][lnk-c-project-properties]. 
7. Cliquez sur le dossier **Linker**, puis cliquez sur la page de propriétés **d’entrée**.
8. Ajoutez **crypt32.lib** à la propriété **Dépendances supplémentaires**. Cliquez sur **OK**, puis de nouveau sur **OK** pour enregistrer les valeurs des propriétés du projet.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Spécification du comportement de l’appareil IoT Hub
Les bibliothèques clientes d’IoT Hub utilisent un modèle pour spécifier le format des messages que l’appareil envoie à IoT Hub et les commandes qu’il reçoit d’IoT Hub.

1. Dans Visual Studio, ouvrez le fichier RMDevice.c. Remplacez les instructions existantes `#include` par ce qui suit :
   
    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```
2. Ajoutez les déclarations de variables suivantes après les instructions `#include` . Remplacez les valeurs d’espace réservé [Id d’appareil] et [Clé d’appareil] par les valeurs de votre appareil provenant du tableau de bord de la solution de surveillance à distance. Utilisez le nom d’hôte IoT Hub du tableau de bord pour remplacer [Nom IoTHub]. Par exemple, si votre nom d’hôte IoT Hub est **contoso.azure-devices.net**, remplacez [Nom Hub IoT] par **contoso** :
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```
3. Ajoutez le code suivant pour définir le modèle qui permet à l’appareil de communiquer avec IoT Hub. Ce modèle spécifie que l’appareil envoie la température, la température externe, l’humidité et un ID d’appareil en tant que données de télémétrie. L’appareil envoie également des métadonnées sur l’appareil à IoT Hub, notamment une liste des commandes prises en charge par l’appareil. Cet appareil répond aux commandes **SetTemperature** et **SetHumidity** :
   
    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);
   
    DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
    );
   
    DECLARE_STRUCT(DeviceProperties,
    ascii_char_ptr, DeviceID,
    _Bool, HubEnabledState
    );
   
    DECLARE_MODEL(Thermostat,
   
    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),
   
    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),
   
    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
    );
   
    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implémentation du comportement de l’appareil
Ajoutez à présent le code qui implémente le comportement défini dans le modèle.

1. Ajoutez les fonctions suivantes qui s’exécutent lorsque l’appareil reçoit les commandes **SetTemperature** et **SetHumidity** d’IoT Hub :
   
    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }
   
    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```
2. Ajoutez la fonction suivante qui envoie un message à IoT Hub :
   
    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }
   
        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```
3. Ajoutez la fonction suivante qui lie la bibliothèque de sérialisation dans le Kit de développement logiciel (SDK) :
   
    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```
4. Ajoutez la fonction suivante pour la connexion à IoT Hub, l’envoi et la réception de messages, et la déconnexion du Hub. Notez la façon dont l’appareil envoie à IoT Hub les métadonnées le concernant, y compris les commandes qu’il prend en charge, quand il se connecte. Ces métadonnées permettent à la solution de définir l’état de l’appareil sur **En cours d’exécution** sur le tableau de bord :
   
    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;
   
        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;
   
            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {
   
              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;
   
              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);
   
                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }
   
                }
   
                STRING_delete(commandsMetadata);
              }
   
              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;
   
              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;
   
                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);
   
                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }
   
                ThreadAPI_Sleep(1000);
              }
            }
   
            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
   
    Pour référence, voici un exemple de message **DeviceInfo** envoyé à IoT Hub au démarrage :
   
    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
   
    Pour référence, voici un exemple de message **Telemetry** envoyé à IoT Hub :
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
   
    Pour référence, voici un exemple de **commande** provenant d’IoT Hub :
   
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```
5. Remplacez la fonction **principale** par le code suivant pour appeler la fonction **remote_monitoring_run** :
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```
6. Cliquez sur **Générer**, puis sur **Générer la solution** pour générer l’application de l’appareil.
7. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, cliquez sur **Déboguer**, puis cliquez sur **Démarrer une nouvelle instance** pour exécuter l’exemple. La console affiche les messages au fur et à mesure que l’application envoie un exemple de télémétrie à IoT Hub et reçoit des commandes.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx



<!--HONumber=Nov16_HO3-->


