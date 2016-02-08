<properties
   pageTitle="Connectez un périphérique à l'aide de C sur Windows | Microsoft Azure"
   description="Explique comment connecter un appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite en C et exécutée sous Windows."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Connexion de votre appareil à la solution préconfigurée de surveillance à distance IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Générer et exécuter l'exemple de solution C sur Windows

1. Pour cloner le référentiel GitHub des *kits de développement logiciel Microsoft Azure IoT* et installer le *Kit de développement logiciel (SDK) d’appareil Azure IoT pour C* dans votre environnement de bureau Windows, suivez les instructions contenues dans [Configuration de l’environnement de développement Windows][lnk-setup-windows].

2. Dans Visual Studio 2015, ouvrez la solution **remote\_monitoring.sln** dans le dossier **c\\serializer\\samples\\remote\_monitoring\\windows** de votre copie locale du référentiel.

3. Dans l’**Explorateur de solutions**, dans le projet **remote\_monitoring**, ouvrez le fichier **remote\_monitoring.c**.

4. Recherchez le code suivant dans le fichier :

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. Remplacez **[Id de périphérique]** et **[Clé de périphérique]** par les valeurs de votre périphérique provenant du tableau de bord de solution de surveillance à distance.

6. Utilisez le **nom d’hôte IoT Hub** du tableau de bord pour remplacer **[Nom IoTHub]** et **[Suffixe IoTHub, c’est-à-dire azure-devices.net]**. Par exemple, si votre **nom d’hôte d’IoT Hub** est **contoso.azure-devices.net**, remplacez **[Nom IoTHub]** par **contoso** et remplacez **[Suffixe IoTHub, c'est-à-dire azure-devices.net]** par **azure-devices.net** comme indiqué ci-dessous :

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet **remote\_monitoring**, cliquez sur **Déboguer**, puis cliquez sur **Démarrer une nouvelle instance** pour générer et exécuter l’exemple. La console affiche les messages au fur et à mesure que l’application envoie un exemple de télémétrie à IoT Hub.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0128_2016-->