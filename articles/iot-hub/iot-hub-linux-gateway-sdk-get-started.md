---
title: Prise en main du SDK de la passerelle Azure IoT (Linux) | Microsoft Docs
description: "Comment créer une passerelle sur une machine Linux et en savoir plus sur les concepts clés dans le kit de développement logiciel de passerelles IoT Azure, comme les modules et les fichiers de configuration JSON."
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
ms.date: 11/23/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: e1cf5ed3f2434a9e98027afd0225207ad5d2f1b1
ms.openlocfilehash: 28984e14f5afc27b608ab37daf19d454eb7c3201


---
# <a name="get-started-with-the-azure-iot-gateway-sdk-linux"></a>Prise en main du SDK de la passerelle Azure IoT (Linux)
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple
Avant de commencer, vous devez [configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Linux.

1. Ouvrez un interpréteur de commandes.
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
3. Exécutez le script **tools/build.sh** . Ce script utilise l’utilitaire **cmake** pour créer un dossier appelé **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** et pour générer un makefile. Le script crée ensuite la solution, et ignore les tests unitaires et les tests de bout en bout. Ajoutez le paramètre **--run-unittests** si vous souhaitez générer et exécuter les tests unitaires. Ajoutez le paramètre **--run-e2e-tests** si vous souhaitez générer et exécuter les tests de bout en bout.

> [!NOTE]
> Chaque fois que vous exécutez le script **build.sh**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple
1. Le script **build.sh** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **azure-iot-gateway-sdk**. Cela inclut les deux modules utilisés dans cet exemple.
   
    Le script de build place **liblogger.so** dans le dossier **build/modules/logger/** et **libhello_world.so** dans le dossier **build/modules/hello_world/**. Utilisez ces chemins d'accès pour la valeur **module path** comme indiqué dans le fichier de paramètres JSON ci-dessous.
2. Le processus hello_world_sample utilise le chemin d’accès à un fichier de configuration JSON en tant qu’argument dans la ligne de commande. Un exemple de fichier JSON a été fourni dans le cadre du référentiel dans **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** et est copié ci-dessous. Il fonctionnera comme tel, sauf si vous avez modifié le script de build pour placer des modules ou des exécutables de l’exemple dans les emplacements autres que ceux par défaut.

   > [!NOTE]
   > Les chemins d’accès du module sont relatifs au répertoire de travail actuel à partir duquel l’exécutable hello_world_sample est lancé, et non au répertoire où se trouve l’exécutable. L’exemple de fichier de configuration JSON écrit par défaut « log.txt » dans votre répertoire de travail actuel.
   
    ```
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
3. Accédez au dossier **azure-iot-gateway-sdk/build**.
4. Exécutez la commande suivante :
   
   ```
   ./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Feb17_HO3-->


