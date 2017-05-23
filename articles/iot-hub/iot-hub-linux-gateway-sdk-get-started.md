---
title: "Prise en main de Azure IoT Edge (Linux) | Microsoft Docs"
description: "Comment créer une passerelle sur une machine Linux et en savoir plus sur les concepts clés dans Azure IoT Edge, comme les modules et les fichiers de configuration JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c401052d65cb9f06398fcf6597dc1de5e7451734
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorer l’architecture de Azure IoT Edge sur Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple

Avant de commencer, vous devez [configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Linux.

1. Ouvrez un interpréteur de commandes.
1. Accédez au dossier racine de votre copie locale du référentiel **iot-edge**.
1. Exécutez le script **tools/build.sh** . Ce script utilise l’utilitaire **cmake** pour créer un dossier appelé **build** dans le dossier racine de votre copie locale du référentiel **iot-edge** et pour générer un makefile. Le script crée ensuite la solution, et ignore les tests unitaires et les tests de bout en bout. Ajoutez le paramètre **--run-unittests** si vous souhaitez générer et exécuter les tests unitaires. Ajoutez le paramètre **--run-e2e-tests** si vous souhaitez générer et exécuter les tests de bout en bout.

> [!NOTE]
> Chaque fois que vous exécutez le script **build.sh**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **iot-edge**.

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple

1. Le script **build.sh** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Cette sortie inclut les deux modules IoT Edge utilisés dans cet exemple.

    Le script place **liblogger.so** dans le dossier **build/modules/logger/** et **libhello\_world.so** dans le dossier **build/modules/hello_world/**. Utilisez ces chemins d’accès pour la valeur **module path** comme indiqué dans le fichier d’exemple de paramètres JSON suivant.
1. Le processus hello\_world\_sample utilise le chemin d’accès à un fichier de configuration JSON en tant qu’argument de ligne de commande. Le fichier d’exemple JSON suivant est fourni dans le référentiel du Kit de développement logiciel (SDK) à **samples/hello\_world/src/hello\_world\_lin.json**. Ce fichier de configuration fonctionne comme tel, sauf si vous modifiez le script de build pour placer des modules IoT Edge ou des exécutables de l’exemple dans les emplacements autres que ceux par défaut.

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
1. Accédez au dossier **build**.
1. Exécutez la commande suivante :

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

