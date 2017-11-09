---
title: Configurer un appareil pour le service IoT Hub Device Provisioning | Microsoft Docs
description: "Configurer un appareil à provisionner par le biais du service IoT Hub Device Provisioning pendant le processus de fabrication de l’appareil"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c99279413b50e7bf1e6058a4151890e3a8f83892
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configurer un appareil à provisionner à l’aide du service IoT Hub Device Provisioning

Dans le didacticiel précédent, vous avez appris à configurer le service IoT Hub Device Provisioning afin de provisionner automatiquement vos appareils pour votre hub IoT. Ce didacticiel fournit des instructions pour configurer votre appareil pendant le processus de fabrication, afin que vous puissiez configurer le service Device Provisioning pour votre appareil en fonction de son [module de sécurité matériel (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) et que l’appareil puisse se connecter à votre service Device Provisioning quand il démarre pour la première fois. Ce didacticiel présente les processus correspondant aux opérations suivantes :

> [!div class="checklist"]
> * Sélectionner un module de sécurité matériel
> * Générer le SDK client du service Device Provisioning pour le module HSM sélectionné
> * Extraire les artefacts de sécurité
> * Paramétrer la configuration du service Device Provisioning sur l’appareil

## <a name="prerequisites"></a>Prérequis

Avant de continuer, créez votre instance du service Device Provisioning et un hub IoT en suivant les instructions indiquées dans le didacticiel [Configurer le cloud pour le provisionnement d’appareils](./tutorial-set-up-cloud.md).


## <a name="select-a-hardware-security-module"></a>Sélectionner un module de sécurité matériel

Le [SDK client du service Device Provisioning](https://github.com/Azure/azure-iot-sdk-c/tree/master/dps_client) prend en charge deux types de modules de sécurité matériels (ou HSM) : 

- [Module de plateforme sécurisée (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - Le module TPM est une norme établie pour la plupart des plateformes d’appareils Windows, ainsi que pour quelques appareils Linux/Ubuntu. En tant que fabricant d’appareil, vous pouvez choisir ce module HSM si un de ces systèmes d’exploitation s’exécute sur vos appareils et que vous recherchez une norme établie pour les modules HSM. Avec les processeurs TPM, vous pouvez uniquement inscrire chaque appareil individuellement auprès du service Device Provisioning. À des fins de développement, vous pouvez utiliser le simulateur TPM sur votre machine de développement Windows ou Linux.

- Modules de sécurité matériels basés sur [X.509](https://cryptography.io/en/latest/x509/). 
    - Les modules HSM basés sur X.509 sont des processeurs assez récents ; Microsoft travaille au développement de processeurs RIoT ou DICE qui implémentent les certificats X.509. Avec les processeurs X.509, vous pouvez effectuer une inscription en bloc dans le portail. En outre, certains systèmes non-Windows, comme embedOS, sont pris en charge. À des fins de développement, le SDK client du service Device Provisioning prend en charge un simulateur d’appareil X.509. 

En tant que fabricant d’appareil, vous devez sélectionner les processeurs/modules de sécurité matériels qui sont basés sur l’un des types précédents. Les autres types de modules HSM ne sont pas pris en charge dans le SDK client du service Device Provisioning.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>Générer le SDK client du service Device Provisioning pour le module HSM sélectionné

Le SDK client du service Device Provisioning permet d’implémenter le mécanisme de sécurité sélectionné dans le logiciel. Les étapes suivantes montrent comment utiliser le SDK pour le processeur HSM sélectionné :

1. Si vous avez suivi le [Démarrage rapide pour créer un appareil simulé](./quick-create-simulated-device.md), vous disposez de la configuration nécessaire pour générer le SDK. Dans le cas contraire, suivez les quatre premières étapes de la section [Préparer l’environnement de développement](./quick-create-simulated-device.md#setupdevbox). Ces étapes permettent de cloner le dépôt GitHub pour le SDK client du service Device Provisioning et d’installer l’outil de génération `cmake`. 

1. Générez le SDK pour le type de module HSM que vous avez sélectionné pour votre appareil, à l’aide de l’une des commandes suivantes depuis l’invite de commandes :
    - Pour les appareils TPM :
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - Pour le simulateur TPM :
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - Pour le simulateur et les appareils X.509 :
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. Le SDK fournit la prise en charge par défaut des appareils exécutant des implémentations Windows ou Ubuntu pour le module TPM et les modules HSM X.509. Pour les modules HSM pris en charge, passez à la section [Extraire les artefacts de sécurité](#extractsecurity) ci-dessous. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Prendre en charge les appareils TPM et X.509 personnalisés

Le SDK client du service Device Provisioning ne fournit pas de prise en charge par défaut pour les appareils TPM et X.509 qui n’exécutent pas Windows ou Ubuntu. Pour ces appareils, vous devez écrire du code personnalisé pour votre propre processeur HSM, comme indiqué dans les étapes suivantes :

### <a name="develop-your-custom-repository"></a>Développer votre dépôt personnalisé

1. Développez un dépôt GitHub pour accéder à votre module HSM. Ce projet doit produire une bibliothèque statique destinée au SDK Device Provisioning.
1. Votre bibliothèque doit implémenter les fonctions définies dans le fichier d’en-tête suivant : a. Pour les appareils TPM personnalisés, implémentez les fonctions définies dans `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_tpm_impl.h`.
    b. Pour les appareils X.509 personnalisés, implémentez les fonctions définies dans `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_x509_impl.h`. 
1. En outre, votre dépôt HSM doit contenir un fichier `CMakeLists.txt` à la racine du dépôt à générer.

### <a name="integrate-with-the-device-provisioning-service-client"></a>Effectuer l’intégration au client du service Device Provisioning

Une fois votre bibliothèque correctement générée, vous pouvez accéder au kit IoThub C-SDK pour y mettre votre dépôt :

1. Indiquez le dépôt GitHub HSM personnalisé, le chemin de la bibliothèque et son nom dans la commande cmake suivante :
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   Dans cette commande, remplacez `<custom_hsm>` par `tpm` ou `x509`. Cette commande crée un marqueur pour votre dépôt HSM personnalisé à l’intérieur du répertoire `cmake`. Notez que le module HSM personnalisé doit toujours être basé sur les mécanismes de sécurité TPM ou X.509.

1. Ouvrez le SDK dans Visual Studio et générez-le. 

    - Le processus de génération clone le dépôt personnalisé et génère la bibliothèque.
    - Le SDK essaie d’établir un lien avec module HSM personnalisé défini dans la commande cmake.

1. Exécutez l’exemple `\azure-iot-sdk-c\dps_client\samples\dps_client_sample\dps_client_sample.c` pour vérifier si votre module HSM est implémenté correctement.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Extraire les artefacts de sécurité

L’étape suivante consiste à extraire les artefacts de sécurité pour le module HSM sur votre appareil.

1. Pour un appareil TPM, vous devez récupérer la **paire de clés de type EK** qui lui est associée auprès du fabricant du processeur TPM. Vous pouvez dériver un **ID d’inscription** unique pour votre appareil TPM en hachant la paire de clés de type EK. 
2. Pour un appareil X.509, vous devez obtenir les certificats délivrés à votre ou vos appareils : certificats d’entité finale pour les inscriptions d’appareils individuels, ou certificats racines pour les inscriptions de groupe d’appareils.

Ces artefacts de sécurité sont obligatoires pour inscrire vos appareils auprès du service Device Provisioning. Ensuite, le service de provisionnement attend qu’un de ces appareils démarre et se connecte à lui. Consultez [Guide pratique pour gérer les inscriptions d’appareils](how-to-manage-enrollments.md) pour plus d’informations sur la création d’inscriptions à l’aide de ces artefacts de sécurité. 

Au premier démarrage de votre appareil, le SDK client interagit avec votre processeur pour extraire les artefacts de sécurité de l’appareil et vérifie l’inscription auprès de votre service Device Provisionings. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Paramétrer la configuration du service Device Provisioning sur l’appareil

La dernière étape du processus de fabrication d’un appareil consiste à écrire une application qui utilise le SDK client du service Device Provisioning pour inscrire l’appareil auprès du service. Ce SDK fournit les API suivantes à vos applications :

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

N’oubliez pas d’initialiser les variables `dps_uri` et `dps_scope_id` avant de les utiliser, comme indiqué dans la [section Simuler la première séquence de démarrage de l’appareil de ce guide de démarrage rapide](./quick-create-simulated-device.md#firstbootsequence). L’API d’enregistrement client Device Provisioning `DPS_Client_LL_Create` se connecte au service Device Provisioning global. *L’étendue de l’ID* est générée par le service et garantit l’unicité. Elle est immuable et sert à identifier les ID d’inscription. Le `iothub_uri` permet à l’API d’enregistrement client IoT Hub `IoTHubClient_LL_CreateFromDeviceAuth` de se connecter au hub IoT adéquat. 


À l’aide de ces API, votre appareil peut se connecter pour être enregistré auprès du service Device Provisioning quand il démarre, obtenir les informations concernant votre hub IoT, puis s’y connecter. Le fichier `dps_client/samples/dps_client_sample/dps_client_sample.c` montre comment utiliser ces API. En règle générale, vous devez créer le framework suivant pour l’enregistrement client :

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

Vous pouvez affiner votre application d’enregistrement client du service Device Provisioning à l’aide d’un appareil simulé dans un premier temps, au moyen d’une configuration de service de test. Une fois que votre application fonctionne dans l’environnement de test, vous pouvez la générer pour votre appareil et copier le fichier exécutable sur l’image de votre appareil. Ne démarrez pas l’appareil tout de suite ; vous devez au préalable [l’inscrire auprès du service Device Provisioning](./tutorial-provision-device-to-hub.md#enrolldevice). Consultez les étapes ci-dessous pour étudier ce processus. 

## <a name="clean-up-resources"></a>Supprimer des ressources

À ce stade, vous pouvez avoir configuré les services Device Provisioning et IoT Hub dans le portail. Si vous souhaitez abandonner la configuration du provisionnement d’appareils et/ou retarder l’utilisation d’un de ces services, nous vous recommandons de les fermer pour éviter des coûts inutiles.

1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Dans la partie supérieure du panneau **Toutes les ressources**, cliquez sur **Supprimer**.  
1. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Dans la partie supérieure du panneau **Toutes les ressources**, cliquez sur **Supprimer**.  


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Sélectionner un module de sécurité matériel
> * Générer le SDK client du service Device Provisioning pour le module HSM sélectionné
> * Extraire les artefacts de sécurité
> * Paramétrer la configuration du service Device Provisioning sur l’appareil

Passez au didacticiel suivant afin d’apprendre à provisionner l’appareil pour votre hub IoT en l’inscrivant auprès du service IoT Hub Device Provisioning à des fins de provisionnement automatique.

> [!div class="nextstepaction"]
> [Provisionner l’appareil pour votre hub IoT](tutorial-provision-device-to-hub.md)

