---
title: "Procédure Azure : utiliser différents modules de sécurité matériels avec le Kit de développement logiciel (SDK) Device Provisioning Service Client dans Azure | Microsoft Docs"
description: "Procédure Azure : utiliser différents modules de sécurité matériels avec le Kit de développement logiciel (SDK) Device Provisioning Service Client dans Azure"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 184bbdc0a6bef74d0e5ac79afe3858354c6b1695
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Utilisation de différents modules de sécurité matériels avec le kit de développement logiciel (SDK) Device Provisioning Service Client
Ces étapes vous montrent comment utiliser différents [modules de sécurité matériels (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) avec le kit de développement logiciel (SDK) Device Provisioning Service Client en C à l’aide d’un simulateur et d’un appareil physique.  Le service d’approvisionnement prend en charge deux modes d’authentification : X**.**509 et Module de plateforme sécurisée (TPM).

## <a name="prerequisites"></a>Composants requis

Préparez votre environnement de développement en suivant la section intitulée « Préparer l’environnement de développement » dans le guide [Créer et approvisionner un appareil simulé à l’aide du service d’approvisionnement d’appareil Azure IoT Hub (préversion)] (./quick-create-simulated-device.md).

## <a name="enable-authentication-with-different-hsms"></a>Activer l’authentification avec différents HSM

Le mode d’authentification (X**.**509 ou TPM) doit être activé pour le simulateur ou l’appareil physique avant qu’ils puissent être inscrits dans le portail Azure.  Accédez au dossier racine pour azure-iot-sdk-c.  Exécutez la commande spécifiée selon le mode d’authentification que vous choisissez.

### <a name="use-x509-with-simulator"></a>Utiliser X**.**509 avec le simulateur

Le service d’approvisionnement est fourni avec un émulateur de moteur de composition d’identité d’appareil qui génère un certificat X**.**509 pour l’authentification de l’appareil.  Exécutez la commande suivante pour activer l’authentification X**.**509 :

```
cmake -Ddps_auth_type=x509 ..
```

Vous pouvez trouver des informations sur le matériel avec le moteur de composition d’identité d’appareil [ici](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Utiliser X**.**509 avec le matériel

Le service d’approvisionnement peut être utilisé avec X**.**509 sur un autre matériel.  Une interface entre le matériel et le kit de développement logiciel (SDK) est nécessaire pour établir la connexion.  Contactez le fabricant de votre HSM pour plus d’informations sur l’interface.

### <a name="use-tpm"></a>Utiliser TPM

Le service d’approvisionnement peut se connecter aux processeurs TPM de matériels Windows et Linux avec un jeton SAP.  Exécutez la commande suivante pour activer l’authentification TPM :

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Utiliser TPM avec un simulateur

Si vous n’avez pas d’appareil avec des processeurs TPM, vous pouvez utiliser un simulateur à des fins de développement sur le système d’exploitation Windows.  Exécutez la commande suivante pour activer l’authentification TPM et exécuter le simulateur TPM :

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Générer le kit de développement logiciel (SDK)
Générez le kit de développement logiciel (SDK) avant de créer l’inscription d’appareils.

### <a name="linux"></a>Linux
- Pour générer le kit de développement logiciel (SDK) dans Linux :
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Pour générer des fichiers binaires de débogage, ajoutez l’option CMake correspondante à la commande de génération de projet, par exemple :
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Il existe de nombreuses [options de configuration CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) disponibles pour générer le kit de développement logiciel (SDK). Par exemple, vous pouvez désactiver l’une des piles de protocoles disponibles en ajoutant un argument à la commande de génération de projet CMake :
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Vous pouvez également générer et exécuter un test unitaire :
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Pour générer le kit de développement logiciel (SDK) dans Windows, procédez comme suit pour générer des fichiers de projet :
    - Ouvrez une « invite de commandes développeur pour VS2015 ».
    - Exécutez les commandes CMake suivantes à partir de la racine du référentiel :
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Cette commande génère des bibliothèques x86. Pour effectuer une génération pour x64, modifiez l’argument de générateur Cmake : 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Si la génération du projet se déroule correctement, vous devez voir un fichier de solution Visual Studio (.sln) sous le dossier `cmake`. Pour générer le kit de développement logiciel (SDK) :
    - Ouvrez **cmake\azure_iot_sdks.sln** dans Visual Studio et générez-le, **OU**
    - Exécutez la commande suivante dans l’invite de commandes que vous avez utilisée pour générer les fichiers de projet :
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Pour générer des fichiers binaires de débogage, utilisez l’argument MSBuild correspondant : 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Il existe de nombreuses options de configuration CMake disponibles pour générer le kit de développement logiciel (SDK). Par exemple, vous pouvez désactiver l’une des piles de protocoles disponibles en ajoutant un argument à la commande de génération de projet CMake :
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Vous pouvez également générer et exécuter des tests unitaires :
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliothèques à inclure
- Ces bibliothèques doivent être incluses dans votre kit de développement logiciel (SDK) :
    - Le service d’approvisionnement : dps_http_transport, dps_client, dps_security_client
    - IoTHub Security : iothub_security_client

## <a name="create-a-device-enrollment-entry-in-dps"></a>Créer une entrée d’inscription d’appareil dans DPS

### <a name="tpm"></a>TPM
Si vous utilisez TPM, suivez les instructions de [« Créer et approvisionner un appareil simulé à l’aide du service d’approvisionnement d’appareil Azure IoT Hub (préversion) »](./quick-create-simulated-device.md) pour créer une entrée d’inscription d’appareil dans DPS et simuler le premier démarrage.

### <a name="x509"></a>X**.**509
1. Pour inscrire un appareil dans le service d’approvisionnement, vous devez noter la paire de clé de type EK et l’ID d’inscription de chaque appareil, qui sont affichés dans l’outil d’approvisionnement fourni par le kit de développement logiciel (SDK) Client. Exécutez la commande suivante pour imprimer le certificat d’autorité de certification racine (pour les groupes d’inscription) et le certificat de signataire (pour l’inscription individuelle) :
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre service DPS.
   - Inscription individuelle X**.**509 : dans le panneau de résumé du service d’approvisionnement, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions individuelles** et cliquez sur le bouton **Ajouter** dans la partie supérieure. Sélectionnez **X**.**509** en tant que *mécanisme* d’attestation d’identité, chargez le certificat de signataire comme requis par le panneau. Cela fait, cliquez sur le bouton **Enregistrer**. 
   - Inscription de groupe X**.**509 : dans le panneau de résumé du service d’approvisionnement, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions de groupe** et cliquez sur le bouton **Ajouter** dans la partie supérieure. Sélectionnez **X**.**509** en tant que *mécanisme* d’attestation d’identité, entrez un nom de groupe et un nom de certification, chargez le certificat d’autorité de certification racine comme requis par le panneau. Cela fait, cliquez sur le bouton **Enregistrer**. 

## <a name="connecting-to-iot-hub-after-provisioning"></a>Connexion à IoT Hub après l’approvisionnement

Une fois l’appareil approvisionné avec le service d’approvisionnement, cette API utilise le mode d’authentification HSM pour se connecter avec IoT Hub : 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
