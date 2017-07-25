---
title: "Prise en main d’Azure IoT Edge (Windows) | Microsoft Docs"
description: "Comment créer une passerelle Azure IoT Edge sur une machine Windows et en savoir plus sur les concepts clés dans Azure IoT Edge, comme les modules et les fichiers de configuration JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 5db39bab8e31a8e7026b34e72b4614b0f6f57772
ms.contentlocale: fr-fr
ms.lasthandoff: 06/09/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorer l’architecture de Azure IoT Edge sur Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple

Le script **build.cmd** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Cette sortie inclut les deux modules IoT Edge utilisés dans cet exemple.

Le script de génération place **logger.dll** dans le dossier **build\\modules\\logger\\Debug**, et **hello\_world.dll** dans le dossier **build\\modules\\hello_world\\Debug**. Utilisez ces chemins pour les valeurs de **module path** comme indiqué dans le fichier de paramètres JSON suivant.

Le processus hello\_world\_sample utilise le chemin vers un fichier de configuration JSON comme argument de ligne de commande. L’exemple de fichier JSON suivant est fourni dans le référentiel du SDK dans **samples\\hello\_world\\src\\hello\_world\_wlin.json**. Ce fichier config fonctionne comme tel, sauf si vous modifiez le script de build pour placer les modules IoT Edge ou des exemples d’exécutables dans des emplacements autres que ceux par défaut.

> [!NOTE]
> Les chemins de module sont relatif au répertoire où se trouve le fichier hello\_world\_sample.exe. L’exemple de fichier de configuration JSON écrit par défaut « log.txt » dans votre répertoire de travail actuel.

```json
{
  "modules": [
    {
      "name": "logger",
      "loader": {
        "name": "native",
        "entrypoint": {
          "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
        }
      },
      "args": { "filename": "log.txt" }
    },
    {
      "name": "hello_world",
      "loader": {
        "name": "native",
        "entrypoint": {
          "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
        }
      },
      "args": null
      }
  ],
  "links": [
    {
      "source": "hello_world",
      "sink": "logger"
    }
  ]
}
```

1. Accédez au dossier **build** à la racine de votre copie locale du référentiel **iot-edge**.

1. Exécutez la commande suivante :

    ```cmd
    samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

