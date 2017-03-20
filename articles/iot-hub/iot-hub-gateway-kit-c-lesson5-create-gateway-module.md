---
title: "Créer votre premier module de passerelle Azure IoT | Microsoft Docs"
description: "Créez un module et ajoutez-le à un exemple d’application pour personnaliser les comportements du module."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 027597c73d7282ef929363bab904e7d3d423d3d4
ms.openlocfilehash: db2757015cf8e821cffcfe39bcbd1559c23fc7ed
ms.lasthandoff: 02/23/2017


---
# <a name="lesson-5-create-your-first-azure-iot-gateway-module"></a>Leçon 5 : Créer votre premier module de passerelle Azure IoT
Bien que le Kit de développement de passerelle permette de développer des modules écrits en Java, .NET ou Node.js, ce didacticiel vous guide à travers les étapes de création d’un module en langage C.

## <a name="what-you-will-do"></a>Procédure à suivre

- Compilez et exécutez l’exemple d’application hello_world sur l’Intel NUC.
- Créez un module et compilez-le sur l’Intel NUC.
- Ajouter le nouveau module à l’exemple d’application hello_world, puis exécutez l’exemple sur l’Intel NUC. Le nouveau module affiche des messages « hello world » avec un horodatage.

## <a name="what-you-will-learn"></a>Contenu

- Compilation et exécution d’un exemple d’application sur l’Intel NUC.
- Création d’un module.
- Ajout d’un module à un exemple d’application.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Le Kit de développement logiciel (SDK) de la passerelle Azure IoT qui a été installé sur votre ordinateur hôte.

## <a name="folder-structure"></a>Structure de dossiers

Dans le sous-dossier de la leçon 5 pour l’exemple de code que vous avez cloné dans la leçon 1, il y a un dossier `module` et un dossier `sample`.

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- Le dossier `module/my_module` contient le code source et le script permettant de générer le module.
- Le dossier `sample` contient le code source et le script permettant de générer l’exemple d’application.

## <a name="compile-and-run-the-helloworld-sample-app-on-intel-nuc"></a>Compiler et exécuter l’exemple d’application hello_world sur l’Intel NUC

L’exemple `hello_world` crée une passerelle en fonction du fichier `hello_world.json` qui spécifie les deux modules prédéfinis associés à l’application. La passerelle consigne un message « hello world » dans un fichier toutes les 5 secondes. Dans cette section, vous compilez et exécutez l’application `hello_world` avec son module par défaut.

Pour compiler et exécuter l’application `hello_world`, procédez comme suit sur votre ordinateur hôte :

1. Initialisez les fichiers de configuration en exécutant les commandes suivantes :

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. Mettez à jour le fichier de configuration de la passerelle avec l’adresse MAC de l’Intel NUC. Ignorez cette étape si vous avez suivi la leçon pour [configurer et exécuter un exemple d’application BLE][config_ble].

   1. Ouvrez le fichier de configuration de la passerelle en exécutant la commande suivante :

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. Mettez à jour l’adresse MAC de la passerelle lorsque vous [configurez l’Intel NUC en tant que passerelle IoT][setup_nuc], puis enregistrez le fichier.

1. Compilez l’exemple de code source en exécutant la commande suivante :

   ```bash
   gulp compile
   ```

   La commande transfère l’exemple de code source à l’Intel NUC et exécute `build.sh` pour le compiler.

1. Exécutez l’application `hello_world` sur l’Intel NUC à l’aide de la commande suivante :

   ```bash
   gulp run
   ```

   Cette commande exécute le fichier `../Tools/run-hello-world.js` spécifié dans `config.json` pour démarrer l’exemple d’application sur l’Intel NUC.

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

## <a name="create-a-new-module-and-compile-it-on-intel-nuc"></a>Créer un module et le compiler sur l’Intel NUC

La procédure ci-dessous vous guide dans la création d’un module et sa compilation sur l’Intel NUC. Le module affiche des messages avec un horodatage au moment de leur réception. Vous allez créer votre premier module de passerelle personnalisé dans cette section.

Tout module du Kit de développement logiciel (SDK) de la passerelle Azure IoT doit implémenter les interfaces suivantes :

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

Vous avez également la possibilité d’implémenter l’interface suivante :

   ```C
   pfModule_Start Module_Start
   ```

Le schéma suivant illustre les chemins d’état importants d’un module. Les rectangles représentent les méthodes que vous implémentez pour exécuter des opérations lorsque le module passe d’un état à un autre. Les ovales correspondent aux états principaux dans lesquels le module peut se trouver.

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

Maintenant, nous allons créer un module basé sur le modèle :

1. Ouvrez le dossier du modèle en exécutant la commande suivante :

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` joue un rôle de modèle qui facilite la création d’un module. Le modèle déclare les interfaces. Vous avez simplement besoin d’ajouter une logique à la fonction `MyModule_Receive`.
   - `build.sh` est le script permettant de compiler le module sur l’Intel NUC.
1. Ouvrez le fichier `src/my_module.c` et incluez deux fichiers d’en-tête :

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. Ajoutez le code suivant à la fonction `MyModule_Receive` :

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. Mettez à jour le fichier `config.json` pour spécifier le dossier `workspace` sur votre ordinateur hôte et le chemin de déploiement sur l’Intel NUC. Pendant la compilation, les fichiers du dossier `workspace` sont transférés vers le chemin de déploiement.

   1. Ouvrez le fichier `config.json` en exécutant la commande suivante :

      ```bash
      code config.json
      ```

   1. Mettez à jour `config.json` avec la configuration suivante :

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. Compilez le module en exécutant la commande suivante :

   ```bash
   gulp compile
   ```

   La commande transfère le code source à l’Intel NUC et exécute `build.sh` pour le compiler.

## <a name="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc"></a>Ajouter le module à l’exemple d’application hello_world et exécuter l’application sur l’Intel NUC

Pour accomplir cette tâche, procédez comme suit :

1. Répertoriez tous les fichiers binaires de module disponibles (fichiers .so) sur l’Intel NUC en exécutant la commande suivante :

   ```bash
   gulp modules --list
   ```

   Le chemin binaire de `my_module` que vous avez compilé doit être répertorié comme suit :

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   Si vous modifiez le nom d’utilisateur de connexion par défaut dans `config-gateway.json`, le chemin binaire commencera par `home/<your username>` au lieu de `root`.

1. Ajoutez `my_module` à l’exemple d’application `hello_world`:

   1. Ouvrez le fichier `hello_world.json` en exécutant la commande suivante :

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. Ajoutez le code suivant à la section `modules` :

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      La valeur de `module.path` doit être `/root/gateway_sample/module/my_module/build/libmy_module.so`. Le code déclare l’association de `my_module` à la passerelle, ainsi que l’emplacement du fichier binaire de module spécifié dans `module.path`.
   1. Ajoutez le code suivant à la section `links` :

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      Ce code spécifie le transfert des messages du module `hello_world` à `my_module`.

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. Exécutez l’exemple d’application `hello_world` avec la commande suivante :

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   Le paramètre `--config` force le script `run-hello-world.js` à s’exécuter à l’aide du fichier `hello_world.json`.

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

Félicitations ! Vous pouvez maintenant voir le comportement de ce nouveau module, qui affiche simplement des messages « hello world » avec un horodatage, résultat qui diffère de celui du module « hello_world » d’origine.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un module, l’avez ajouté à l’exemple d’application hello_world et fait s’exécuter l’exemple d’application avec le nouveau module sur votre passerelle. Si vous souhaitez en savoir plus sur les modules de passerelle Azure IoT, vous trouverez d’autres exemples de modules ici : [https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules).

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

