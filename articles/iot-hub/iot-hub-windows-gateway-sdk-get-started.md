---
title: "Prise en main du kit de développement logiciel (SDK) de la passerelle Azure IoT (Windows) | Microsoft Docs"
description: "Comment créer une passerelle sur une machine Windows et en savoir plus sur les concepts clés dans le kit de développement logiciel (SDK) de passerelles IoT Azure, comme les modules et les fichiers de configuration JSON."
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
ms.date: 11/16/2016
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: 4b6cb52c3dcc34c70fd3b86031af48a12573cb8b
ms.lasthandoff: 03/02/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-windows"></a>Exploration de l’architecture du SDK Gateway Azure IoT sur Windows
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple
Avant de commencer, vous devez [configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Windows.

1. Ouvrez une **invite de commandes Développeur pour VS2015** .
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
3. Exécutez le script **tools\\build.cmd**. Ce script crée un fichier de solution Visual Studio et génère la solution. Vous trouverez la solution Visual Studio dans le dossier **build** de votre copie locale du référentiel **azure-iot-gateway-sdk**. Des paramètres supplémentaires peuvent être accordés au script pour générer et exécuter des tests unitaires et de bout en bout. Ces paramètres sont **--run-unittests** et **--run-e2e-tests**, respectivement. 

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple
1. Le script **build.cmd** crée un dossier appelé **build** dans votre copie locale du référentiel. Ce dossier contient les deux modules utilisés dans cet exemple.
   
    Le script build place **logger.dll** dans le dossier **build\\modules\\logger\\Debug**, et **hello_world.dll** dans le dossier **build\\modules\\hello_world\\Debug**. Utilisez ces chemins d’accès pour la valeur **module path** comme indiqué dans le fichier de paramètres JSON suivant.
2. Le processus hello_world_sample utilise le chemin d’accès à un fichier de configuration JSON en tant qu’argument dans la ligne de commande. L’exemple de fichier JSON suivant a été fourni dans le cadre du référentiel dans **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json**. Il fonctionne comme tel, sauf si vous avez modifié le script de build pour placer des modules ou des exécutables de l’exemple dans les emplacements autres que ceux par défaut. 

   > [!NOTE]
   > Les chemins d’accès du module sont relatif au répertoire où se trouve le fichier hello_world_sample.exe. L’exemple de fichier de configuration JSON écrit par défaut « log.txt » dans votre répertoire de travail actuel.
   
    ```
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
3. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.

4. Exécutez la commande suivante :
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

