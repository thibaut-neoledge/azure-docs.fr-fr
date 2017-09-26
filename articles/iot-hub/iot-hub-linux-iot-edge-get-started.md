---
title: "Prise en main d’Azure IoT Edge (Linux) | Microsoft Docs"
description: "Comment créer une passerelle sur une machine Linux et en savoir plus sur les concepts clés dans Azure IoT Edge, comme les modules et les fichiers de configuration JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: e2f26c1abe09feff77e1e2633d8bfcf4ca527aee
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorer l’architecture de Azure IoT Edge sur Linux

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple

Le script **build.sh** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Cette sortie inclut les deux modules IoT Edge utilisés dans cet exemple.

Le script place **liblogger.so** dans le dossier **build/modules/logger/** et **libhello\_world.so** dans le dossier **build/modules/hello_world/**. Utilisez ces chemins d’accès pour les valeurs **module path** comme indiqué dans le fichier d’exemple de paramètres JSON suivant.

Le processus hello\_world\_sample utilise le chemin vers un fichier de configuration JSON comme argument de ligne de commande. Le fichier d’exemple JSON suivant est fourni dans le référentiel du Kit de développement logiciel (SDK) à **samples/hello\_world/src/hello\_world\_lin.json**. Ce fichier de configuration fonctionne comme tel, sauf si vous modifiez le script build pour placer des modules IoT Edge ou des exécutables de l’exemple dans des emplacements autres que ceux par défaut.

> [!NOTE]
> Les chemins d’accès du module sont relatifs au répertoire de travail actuel à partir duquel l’exécutable hello\_world\_sample est lancé, et non au répertoire où se trouve l’exécutable. L’exemple de fichier de configuration JSON écrit par défaut « log.txt » dans votre répertoire de travail actuel.

```json
{
    "modules" :
    [
        {
            "name" : "logger",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "name" : "hello_world",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/hello_world/libhello_world.so"
            }
            },
            "args" : null
        }
    ],
    "links":
    [
        {
            "source": "hello_world",
            "sink": "logger"
        }
    ]
}
```

1. Accédez au dossier **build** à la racine de votre copie locale du référentiel **iot-edge**.

1. Exécutez la commande suivante :

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json`
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]


