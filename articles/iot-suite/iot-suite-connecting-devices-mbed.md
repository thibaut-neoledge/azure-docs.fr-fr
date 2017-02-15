---
title: "Connectez un appareil à l’aide de C sur mbed | Microsoft Docs"
description: "Explique comment connecter un appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite en C et exécutée sous mbed."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f2a77ada25d7d6285c62d2f3d1330df5f192713d


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (mbed)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Générer et exécuter l'exemple de solution C
Les instructions qui suivent décrivent les étapes permettant de connecter un appareil [mbed-enabled Freescale FRDM-K64F][lnk-mbed-home] à la solution de surveillance à distance.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Connectez le périphérique à votre réseau et à votre ordinateur de bureau
1. Connectez le périphérique mbed à votre réseau avec un câble Ethernet. Cette étape est nécessaire car l'exemple d'application requiert un accès à internet.
2. Voir [Mise en route avec mbed][lnk-mbed-getstarted] pour connecter votre appareil mbed à votre ordinateur de bureau.
3. Si votre ordinateur de bureau exécute Windows, consultez [Configuration PC][lnk-mbed-pcconnect] pour configurer l’accès aux ports série de votre appareil mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Créez un projet mbed et importez l’exemple de code
1. Dans votre navigateur Web, accédez [au site de développement](https://developer.mbed.org/)mbed.org. Si vous n’êtes pas inscrit, une option servant à créer un compte vous sera présentée (c’est gratuit). Autrement, connectez-vous avec les informations d’identification de votre compte. Cliquez sur **Compilateur** dans le coin supérieur droit de la page. Vous accédez à l’interface *Espace de travail*.
2. Assurez-vous que la plate-forme matérielle que vous utilisez figure dans le coin supérieur droit de la fenêtre, ou cliquez sur l’icône du coin droit pour sélectionner votre plateforme matérielle.
3. Cliquez sur **Importer** dans le menu principal. Cliquez ensuite sur **Cliquez ici** pour procéder à l’importation via un lien URL en regard du logo de globe mbed.
   
    ![][6]
4. Dans la fenêtre contextuelle, entrez le lien de l’exemple de code https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/, puis cliquez sur **Importer**.
   
    ![][7]
5. Vous pouvez voir dans la fenêtre du compilateur mbed que l’importation de ce projet entraîne également l’importation de différentes bibliothèques. Certaines sont fournies et gérées par l’équipe Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), tandis que d’autres sont des bibliothèques tierces disponibles dans le catalogue de bibliothèques mbed.
   
    ![][8]
6. Ouvrez le fichier remote_monitoring\remote_monitoring.c et recherchez le code suivant dans le fichier :
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
7. Remplacez [Device Id] et [Device Key] par les données de votre appareil pour activer le programme d'exemple afin de vous connecter à votre hub IoT. Utilisez le nom d’hôte IoT Hub pour remplacer les espaces réservés [Nom IoTHub] et [Suffixe IoTHub, c’est-à-dire azure-devices.net]. Par exemple, si votre nom d’hôte IoT Hub est **contoso.azure-devices.net**, **contoso** est le **nom du hub (hubName)** et tous les éléments suivants constituent le **suffixe du hub (hubSuffix)** :
   
    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
   
    ![][9]

### <a name="walk-through-the-code"></a>Examinez le code
Si vous êtes intéressé par le fonctionnement du programme, cette section décrit certains éléments clés de l’exemple de code. Si vous souhaitez simplement exécuter le code, passez directement à la rubrique [Créez et exécutez le projet.](#buildandrun).

#### <a name="defining-the-model"></a>Définition du modèle
Cet exemple utilise la bibliothèque du [sérialiseur][lnk-serializer] pour définir un modèle qui spécifie les messages que l’appareil peut envoyer à IoT Hub et recevoir de IoT Hub. Dans cet exemple, l’espace de noms **Contoso** définit un modèle **Thermostat** qui spécifie les données de télémétrie **Temperature**, **ExternalTemperature**, et **Humidity**, ainsi que des métadonnées telles que l’ID de l’appareil, les propriétés de l’appareil et les commandes auxquelles l’appareil répond :

```
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

Les définitions des commandes **SetTemperature** et **SetHumidity** auxquelles l’appareil répond sont liées à la définition du modèle :

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

#### <a name="connecting-the-model-to-the-library"></a>Connexion d'un modèle à la bibliothèque
Les fonctions **sendMessage** et **IoTHubMessage** représentent un code réutilisable pour envoyer la télémétrie à partir de l’appareil et pour connecter des messages de IoT Hub aux gestionnaires de commandes.

#### <a name="the-remotemonitoringrun-function"></a>La fonction remote_monitoring_run
La fonction **main** du programme appelle la fonction **remote_monitoring_run** quand l’application commence à exécuter le comportement de l’appareil comme un client d’appareil IoT Hub. Cette fonction **remote_monitoring_run** se compose essentiellement de paires de fonctions imbriquées :

* **platform\_init** et **platform\_deinit** effectuent des opérations d’initialisation et d’arrêt spécifiques à la plateforme.
* **serializer\_init** et **serializer\_deinit** initialisent et désinitialisent la bibliothèque du sérialiseur.
* **IoTHubClient\_Create** et **IoTHubClient\_Destroy** créent un handle client, **iotHubClientHandle**, en utilisant les informations d’identification de l’appareil pour se connecter à votre IoT hub.

Dans la section principale de la fonction **remote_monitoring_run**, le programme effectue les opérations suivantes à l’aide du handle **iotHubClientHandle** :

* Crée une instance du modèle de thermostat Contoso et définit les rappels de message pour les deux commandes.
* Envoie des informations sur l'appareil lui-même, y compris les commandes prises en charge, à votre hub IoT à l'aide de la bibliothèque du sérialiseur. Quand le hub reçoit ce message, il fait passer l’état de l’appareil dans le tableau de bord de **En attente** à **En cours d’exécution**.
* Démarre une boucle **while** qui envoie chaque seconde les valeurs de température, de température externe et d'humidité au IoT Hub.

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

<a id="buildandrun"/>

### <a name="build-and-run-the-program"></a>Créez et exécutez le projet.
1. Cliquez sur **Compiler** pour générer le programme. Vous pouvez sans risque ignorer les avertissements, mais si le traitement génère des erreurs, corrigez-les avant de continuer.
2. Si le traitement réussit, le site web du compilateur mbed génère un fichier .bin portant le nom de votre projet et le télécharge sur votre ordinateur local. Copier le fichier .bin sur l’appareil. Lorsque le fichier .bin est enregistré sur le périphérique, ce dernier redémarre et exécute le programme contenu dans le fichier .bin. Vous pouvez redémarrer manuellement le programme à tout moment en appuyant sur le bouton de réinitialisation sur le périphérique mbed.
3. Connectez-vous à l’appareil en utilisant une application cliente SSH, tel que PuTTY. Vous pouvez déterminer le port série que votre appareil va utiliser en consultant le Gestionnaire de périphériques Windows.
   
    ![][11]
4. Dans PuTTY, cliquez sur le type de connexion **Série** . Comme l’appareil se connecte généralement à 9 600 bauds, entrez 9 600 dans le champ **Speed** (Vitesse). Cliquez ensuite sur **Ouvrir**.
5. Le programme démarre l’exécution. Il se peut que vous deviez réinitialiser le tableau (appuyez sur Ctrl + Pause ou appuyez sur le bouton de réinitialisation du tableau) si le programme ne démarre pas automatiquement à la connexion.
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer



<!--HONumber=Nov16_HO3-->


