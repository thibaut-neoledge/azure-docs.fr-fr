---
title: "Conversion de données sur une passerelle IoT avec le kit de développement logiciel (SDK) Gateway Azure IoT | Microsoft Docs"
description: "Utilisez la passerelle IoT pour convertir le format des données de capteur via un module personnalisé dans le kit de développement logiciel (SDK) de la passerelle Azure IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "conversion de données de passerelle iot, transformation des données de passerelle iot"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: d7897f1a7eee5fbb04cf43da0d0156a6b8fb11f6
ms.lasthandoff: 04/25/2017


---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-gateway-sdk"></a>Utilisation de la passerelle IoT pour la transformation de données de capteur avec le kit de développement logiciel (SDK) de la passerelle Azure IoT

> [!NOTE]
> Avant de commencer ce didacticiel, assurez-vous que vous avez suivi les leçons suivantes dans l’ordre :
> * [Configurer Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Utilisation de la passerelle IoT pour connecter des objets au cloud - SensorTag pour Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Un des objectifs d’une passerelle IoT consiste à traiter les données collectées avant leur envoi vers le cloud. Le kit de développement logiciel (SDK) de la passerelle Azure IoT introduit des modules qui peuvent être créés et assemblés pour former le flux de travail de traitement de données. Un module reçoit un message, exécute une action sur celui-ci, puis le déplace sur d'autres modules pour un traitement ultérieur.

## <a name="what-you-learn"></a>Contenu

Vous apprenez à créer un module pour convertir les messages de SensorTag en un format différent.

## <a name="what-you-do"></a>Procédure

* Créez un module pour convertir un message reçu au format .json.
* Compilez le module.
* Ajoutez le module à l’exemple d’application BLE à partir du SDK Gateway Azure IoT.
* Exécutez l'exemple d'application.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Les didacticiels suivants effectués dans l’ordre :
  * [Configurer Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Utilisation de la passerelle IoT pour connecter des objets au cloud - SensorTag pour Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* Un client SSH qui s’exécute sur votre ordinateur hôte. PuTTY est recommandé sur Windows. Linux et macOS sont déjà accompagnés d’un client SSH.
* L’adresse IP et le nom d’utilisateur et le mot de passe pour accéder à la passerelle du client SSH.
* Une connexion Internet.

## <a name="create-a-module"></a>Création d’un module

1. Sur l’ordinateur hôte, exécutez le client SSH et connectez-vous à la passerelle IoT.
1. Clonez les fichiers source du module de conversion à partir de GitHub sur le répertoire de base de la passerelle IoT en exécutant les commandes suivantes :

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Il s’agit d’un module natif du kit de développement logiciel (SDK) pour Azure Gateway écrit dans le langage de programmation C. Le module convertit le format des messages reçus dans celui qui suit :

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>Compilation du module

Pour compiler le module, exécutez les commandes suivantes :

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$\/\/" build.sh
# run the build shell script
./build.sh
```

Vous obtenez un fichier `libmy_module.so` une fois la compilation terminée. Notez le chemin d’accès absolu de ce fichier.

## <a name="add-the-module-to-the-ble-sample-application"></a>Ajoutez le module à l’exemple d’application BLE

1. Accédez au dossier d’exemples en exécutant la commande suivante :

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Ouvrez le fichier de configuration en exécutant la commande suivante :

   ```bash
   vi ble_gateway.json
   ```

1. Ajoutez un module en insérant le code suivant à la section `modules`.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Remplacez `[Your libmy_module.so path]` dans le code par le chemin d’accès absolu du fichier libmy_module.so`.
1. Remplacez le code dans la fonction `links` par le code suivant :

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Appuyez sur `ESC`, puis saisissez `:wq` pour enregistrer le fichier.

## <a name="run-the-sample-application"></a>Exécutez l'exemple d'application

1. Mettez le SensorTag sous tension.
1. Définissez la variable d’environnement SSL_CERT_FILE en exécutant la commande suivante :

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Exécutez l’exemple d’application avec le module ajouté en exécutant la commande suivante :

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement utilisé la passerelle IoT pour convertir le message au format .json SensorTag.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
