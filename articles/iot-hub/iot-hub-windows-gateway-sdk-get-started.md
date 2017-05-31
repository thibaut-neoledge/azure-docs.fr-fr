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
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 2a46a3511d4d286c26aa9ca2e4e619414d82be1d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorer l’architecture de Azure IoT Edge sur Windows

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple

Avant de commencer, vous devez [configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Windows.

1. Ouvrez une **invite de commandes développeur pour VS 2015** ou une **invite de commandes développeur pour VS 2017**.
1. Accédez au dossier racine de votre copie locale du référentiel **iot-edge**.
1. Exécutez le script **tools\\build.cmd**. Ce script crée un fichier de solution Visual Studio et génère la solution. Vous trouverez la solution Visual Studio dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Vous pouvez donner des paramètres supplémentaires au script pour générer et exécuter des tests unitaires et de bout en bout. Ces paramètres sont **--run-unittests** et **--run-e2e-tests**, respectivement.

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple

1. Le script **build.cmd** crée un dossier appelé **build** dans votre copie locale du référentiel. Ce dossier contient les deux modules IoT Edge utilisés dans cet exemple.

    Le script de génération place **logger.dll** dans le dossier **build\\modules\\logger\\Debug**, et **hello\_world.dll** dans le dossier **build\\modules\\hello_world\\Debug**. Utilisez ces chemins pour les valeurs de **module path** comme indiqué dans le fichier de paramètres JSON suivant.
1. Le processus hello\_world\_sample utilise le chemin vers un fichier de configuration JSON comme argument de ligne de commande. L’exemple de fichier JSON suivant est fourni dans le référentiel du SDK dans **samples\\hello\_world\\src\\hello\_world\_wlin.json**. Ce fichier de configuration fonctionne comme tel, sauf si vous modifiez le script de build pour placer des modules ou des exécutables de l’exemple dans les emplacements autres que ceux par défaut.

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

1. Accédez au dossier racine de votre copie locale du référentiel **iot-edge**.

1. Exécutez la commande suivante :

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

