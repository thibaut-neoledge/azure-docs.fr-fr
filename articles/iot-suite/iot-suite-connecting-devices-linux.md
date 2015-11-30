<properties
   pageTitle="Connectez un périphérique à l’aide de C sous Linux | Microsoft Azure"
   description="Explique comment connecter un appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite en C et exécutée sous Linux."
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Connexion de votre appareil à la solution préconfigurée de surveillance à distance IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Générer et exécuter l’exemple de solution C sous Linux

1. Pour cloner le référentiel GitHub des *kits de développement logiciel (SDK) Microsoft Azure IoT* et installer le *Kit de développement logiciel (SDK) d’appareil Azure IoT pour C* dans votre environnement de bureau Linux, suivez les instructions contenues dans [Configuration de l’environnement de développement Linux][lnk-setup-linux].

2. Ouvrez le fichier **c/serializer/samples/remote\_monitoring/remote\_monitoring.c** dans un éditeur de texte.

3. Recherchez le code suivant dans le fichier :

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. Remplacez **[Id de périphérique]** et **[Clé de périphérique]** par les valeurs de votre périphérique provenant du tableau de bord de solution de surveillance à distance.

5. Utilisez le **nom d’hôte IoT Hub** du tableau de bord pour remplacer **[Nom IoTHub]** et **[Suffixe IoTHub, c’est-à-dire azure-devices.net]**. Par exemple, si votre **nom d’hôte IoT Hub** est **contoso.azure-devices.net**, remplacez **[Nom IoTHub]** par **contoso** et remplacez **[Suffixe IoTHub, c’est-à-dire azure-devices.net]** par **azure-devices.net** comme indiqué ci-dessous :

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Enregistrez vos modifications et générez les exemples. Pour générer votre exemple, vous pouvez exécuter le script build.sh dans le répertoire **c/build\_all/linux**. Le script crée un dossier **cmake** pour stocker les exemples de programmes compilés.

7. Exécutez l’exemple d’application **cmake/serializer/samples/remote\_monitoring/remote\_monitoring**.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=Nov15_HO4-->