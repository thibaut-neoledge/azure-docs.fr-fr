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
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorer l’architecture de Azure IoT Edge sur Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Exécution de l'exemple

Le script **build.cmd** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Cette sortie inclut de nombreux fichiers, mais cet exemple est axé sur trois de ces fichiers :
- Deux modules IoT Edge : **build\\modules\\logger\\Debug\\logger.dll** et **build\\modules\\hello_world\\Debug\\hello\_world.dll**. 
- Un fichier exécutable : **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**. Ce processus utilise un fichier de configuration JSON comme argument de ligne de commande.

Le quatrième fichier que vous utilisez dans cet exemple ne figure pas dans le dossier build, mais a été inclus lorsque vous avez cloné le référentiel iot-edge :
- Un fichier de configuration JSON : **samples\\hello\_world\\src\\hello\_world\_win.json**. Ce fichier contient les chemins d’accès aux deux modules. Il déclare également l’endroit où logger.dll écrit sa sortie. La valeur par défaut est **log.txt** dans votre répertoire de travail actuel. 

   >[!NOTE]
   >Si vous déplacez les exemples de modules ou que vous ajoutez vos propres exemples à des fins de test, mettez à jour en conséquence les valeurs **module.path** dans le fichier de configuration. Les chemins d’accès aux modules sont relatifs au répertoire dans lequel se trouve **hello\_world\_sample.exe**. 

Pour exécuter l’exemple, procédez comme suit :

1. Accédez au dossier **build** à la racine de votre copie locale du référentiel **iot-edge**.

1. Exécutez la commande suivante :

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. La sortie ci-après signifie que l’exemple s’exécute correctement :

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Appuyez sur la touche **Entrée** clé pour arrêter le processus.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
