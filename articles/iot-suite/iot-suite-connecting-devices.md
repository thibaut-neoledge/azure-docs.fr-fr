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
ms.date: 03/08/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3536777690a9b00ded7c7fdf4d5f39638dad71b0
ms.lasthandoff: 03/10/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Création d’un exemple de solution C sur Windows
Les étapes suivantes vous montrent comment créer une application cliente qui communique avec la solution préconfigurée de surveillance à distance. Cette application est écrite en C, générée et exécutée sur Windows.

Créez un projet de démarrage dans Visual Studio 2015 ou Visual Studio 2017 et ajoutez les packages NuGet clients de l’appareil IoT Hub :

1. Dans Visual Studio, créez une application console C à l’aide du modèle **Application console Win32** de Visual C++. Nommez le projet **RMDevice**.
2. Sur la page **Paramètres de l’application** dans **l’Assistant Application Win32**, assurez-vous que l’option **Application console** est sélectionnée et décochez les cases **En-tête précompilé** et **Vérifications SDL (Security Development Lifecycle)**.
3. Dans l’ **Explorateur de solutions**, supprimez les fichiers stdafx.h, targetver.h et stdafx.cpp.
4. Dans l’ **Explorateur de solutions**, renommez le fichier RMDevice.cpp en RMDevice.c.
5. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, puis cliquez sur **Gérer les packages NuGet**. Cliquez sur **Parcourir**, puis recherchez et installez les packages NuGet suivants :
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, puis cliquez sur **Propriétés** pour ouvrir la boîte de dialogue **Pages de propriétés** du projet. Pour plus d’informations, consultez [Setting Visual C++ Project Properties (Définition des propriétés de projet Visual C++)][lnk-c-project-properties]. 
7. Cliquez sur le dossier **Linker**, puis cliquez sur la page de propriétés **d’entrée**.
8. Ajoutez **crypt32.lib** à la propriété **Dépendances supplémentaires**. Cliquez sur **OK**, puis de nouveau sur **OK** pour enregistrer les valeurs des propriétés du projet.

Ajoutez la bibliothèque JSON Parson au projet **RMDevice** ainsi que les instructions `#include` requises :

1. Dans un dossier approprié sur votre ordinateur, clonez le référentiel GitHub Parson à l’aide de la commande suivante :

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Copiez les fichiers parson.h et parson.c de la copie locale du référentiel Parson dans le dossier de votre projet **RMDevice**.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **RMDevice**, cliquez sur **Ajouter**, puis sur **Élément existant**.

1. Dans la boîte de dialogue **Ajouter un élément existant**, sélectionnez les fichiers parson.h et parson.c dans le dossier du projet **RMDevice**. Cliquez ensuite sur **Ajouter** pour ajouter ces deux fichiers à votre projet.

1. Dans Visual Studio, ouvrez le fichier RMDevice.c. Remplacez les instructions existantes `#include` par ce qui suit :
   
    ```
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > À présent, vous pouvez vérifier que votre projet contient les dépendances appropriées définies en le générant.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Ajoutez du code pour appeler la fonction **remote\_monitoring\_run**, puis générez et exécutez l’application de l’appareil.

1. Remplacez la fonction **main** par le code suivant pour appeler la fonction **remote\_monitoring\_run** :
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Cliquez sur **Générer**, puis sur **Générer la solution** pour générer l’application de l’appareil.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, cliquez sur **Déboguer**, puis cliquez sur **Démarrer une nouvelle instance** pour exécuter l’exemple. La console affiche des messages, car l’application envoie un échantillon de données de télémétrie à la solution préconfigurée, reçoit les valeurs de propriété souhaitées définies dans le tableau de bord de la solution et répond aux méthodes appelées à partir du tableau de bord de la solution.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

