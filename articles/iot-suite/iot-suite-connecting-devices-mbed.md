---
title: "Connectez un appareil à l’aide de C sur mbed | Microsoft Docs"
description: "Explique comment connecter un appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite en C et exécutée sous mbed."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: ef7b78f85a787f8fbe22c0e26aa34f0cd1685d58
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (mbed)

## <a name="scenario-overview"></a>Présentation du scénario
Dans ce scénario, vous allez créer un appareil qui envoie la télémétrie suivante à la [solution préconfigurée][lnk-what-are-preconfig-solutions] de surveillance à distance :

* Température externe
* Température interne
* Humidité

Par souci de simplicité, le code sur l’appareil génère des valeurs d’exemple, mais nous vous encourageons à étendre l'exemple en connectant des capteurs réels à votre appareil et en envoyant une télémétrie réelle.

L’appareil est également en mesure de répondre aux méthodes appelées à partir du tableau de bord de la solution et aux valeurs de propriétés souhaitées définies dans le tableau de bord de la solution.

Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk-free-trial].

## <a name="before-you-start"></a>Avant de commencer
Avant d’écrire du code pour votre appareil, vous devez approvisionner votre solution préconfigurée de surveillance à distance et approvisionner un nouvel appareil personnalisé dans cette solution.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Approvisionner la solution préconfigurée de surveillance à distance
L’appareil que vous créez dans ce didacticiel envoie des données à une instance de la solution préconfigurée de [surveillance à distance][lnk-remote-monitoring]. Si vous n’avez pas déjà approvisionné la solution préconfigurée de surveillance à distance dans votre compte Azure, procédez comme suit :

1. Sur la page <https://www.azureiotsuite.com/>, cliquez sur **+** pour créer une solution.
2. Cliquez sur **Sélectionner** dans le panneau **Surveillance à distance** pour créer votre solution.
3. Sur la page **Create Remote monitoring solution** (Créer une solution de surveillance à distance), entrez le nom de votre choix dans la zone **Nom de solution**, sélectionnez la **Région** dans laquelle vous souhaitez procéder au déploiement, puis sélectionnez l’abonnement Azure à utiliser. Cliquez ensuite sur **Créer la solution**.
4. Attendez la fin du processus de configuration.

> [!WARNING]
> Les solutions préconfigurées utilisent des services Azure facturables. Veillez à supprimer la solution préconfigurée de votre abonnement lorsque vous avez terminé pour éviter toute facturation inutile. Vous pouvez supprimer complètement une solution préconfigurée de votre abonnement à partir de la page <https://www.azureiotsuite.com/>.
> 
> 

Au terme du processus d’approvisionnement de la solution de surveillance à distance, cliquez sur **Lancer** pour ouvrir le tableau de bord de la solution dans votre navigateur.

![Tableau de bord de solution][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Configurer votre appareil dans la solution de surveillance à distance
> [!NOTE]
> Si vous avez déjà approvisionné un appareil dans votre solution, vous pouvez ignorer cette étape. Vous devez connaître les informations d'identification de l’appareil lorsque vous créez l'application cliente.
> 
> 

Pour qu’un appareil puisse se connecter à la solution préconfigurée, il doit s’identifier auprès d’IoT Hub à l’aide d’informations d’identification valides. Vous pouvez récupérer les informations d’identification de l’appareil à partir du tableau de bord de la solution. Les informations d’identification de l’appareil seront ajoutées dans votre application cliente dans la suite de ce didacticiel.

Pour ajouter un appareil à votre solution de surveillance à distance, procédez comme suit dans le tableau de bord de la solution :

1. Dans le coin inférieur gauche du tableau de bord, cliquez sur **Ajouter un périphérique**.
   
   ![Ajout d’un appareil][1]
2. Dans le panneau **Appareil personnalisé**, cliquez sur **Ajouter nouveau**.
   
   ![Ajout d’un appareil personnalisé][2]
3. Choisissez **Me laisser définir mon propre ID d'appareil**. Entrez un ID d’appareil comme **monappareil**, cliquez sur **Vérifier l’ID** pour vous assurer que ce nom n’est pas déjà utilisé, puis cliquez sur **Créer** pour approvisionner l’appareil.
   
   ![Ajouter ID d’appareil][3]
4. Prenez note des informations d’identification de l’appareil (ID d’appareil, nom d’hôte IoT Hub et clé d’appareil). Votre application cliente a besoin de ces valeurs pour se connecter à la solution de surveillance à distance. Cliquez ensuite sur **Terminé**.
   
    ![Afficher les informations d’identification d’un appareil][4]
5. Sélectionnez votre appareil dans la liste d’appareils du tableau de bord de la solution. Ensuite, dans le panneau **Détails de l’appareil**, cliquez sur **Activer l’appareil**. L’état de votre appareil est maintenant **En cours d’exécution**. La solution de surveillance à distance peut désormais recevoir des données de télémétrie à partir de votre appareil et appeler des méthodes sur l’appareil.

[img-dashboard]: ./media/iot-suite-connecting-devices-mbed/dashboard.png
[1]: ./media/iot-suite-connecting-devices-mbed/suite0.png
[2]: ./media/iot-suite-connecting-devices-mbed/suite1.png
[3]: ./media/iot-suite-connecting-devices-mbed/suite2.png
[4]: ./media/iot-suite-connecting-devices-mbed/suite3.png

[lnk-what-are-preconfig-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

## <a name="build-and-run-the-c-sample-solution"></a>Générer et exécuter l'exemple de solution C

Les instructions qui suivent décrivent la procédure de connexion d’un appareil [mbed-enabled Freescale FRDM-K64F][lnk-mbed-home] à la solution de surveillance à distance.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Connectez le périphérique à votre réseau et à votre ordinateur de bureau

1. Connectez le périphérique mbed à votre réseau avec un câble Ethernet. Cette étape est nécessaire car l'exemple d'application requiert un accès à internet.

1. Voir [Getting Started with mbed (Prise en main de mbed)][lnk-mbed-getstarted] pour connecter votre appareil mbed à votre ordinateur de bureau.

1. Si votre ordinateur de bureau exécute Windows, consultez [Configuration PC][lnk-mbed-pcconnect] pour configurer l’accès aux ports série de votre appareil mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Créez un projet mbed et importez l’exemple de code

Procédez comme suit pour ajouter un exemple de code à un projet mbed. Vous importez le projet de démarrage de surveillance à distance avant de le modifier pour utiliser le protocole MQTT à la place du protocole AMQP. Pour le moment, vous devez utiliser le protocole MQTT pour utiliser les fonctionnalités de gestion des appareils de IoT Hub.

1. Dans votre navigateur Web, accédez [au site de développement](https://developer.mbed.org/)mbed.org. Si vous n’êtes pas inscrit, une option servant à créer un compte vous sera présentée (c’est gratuit). Autrement, connectez-vous avec les informations d’identification de votre compte. Cliquez sur **Compilateur** dans le coin supérieur droit de la page. Vous accédez à l’interface *Espace de travail*.

1. Assurez-vous que la plate-forme matérielle que vous utilisez figure dans le coin supérieur droit de la fenêtre, ou cliquez sur l’icône du coin droit pour sélectionner votre plateforme matérielle.

1. Cliquez sur **Importer** dans le menu principal. Cliquez ensuite sur **Click here to import from URL (Cliquez ici pour importer à partir de l’URL)**.
   
    ![Commencer l’importation vers l’espace de travail mbed][6]

1. Dans la fenêtre contextuelle, entrez le lien de l’exemple de code https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/, puis cliquez sur **Importer**.
   
    ![Importer un exemple de code dans l’espace de travail mbed][7]

1. Vous pouvez voir dans la fenêtre du compilateur mbed que l’importation de ce projet entraîne également l’importation de différentes bibliothèques. Certaines sont fournies et gérées par l’équipe Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), tandis que d’autres sont des bibliothèques tierces disponibles dans le catalogue de bibliothèques mbed.
   
    ![Afficher le projet mbed][8]

1. Dans le **Program Workspace (Espace de travail du programme)**, cliquez avec le bouton droit sur la bibliothèque **iothub\_amqp\_transport**, puis cliquez sur **Delete (Supprimer)** et sur **OK** pour confirmer.

1. Dans le **Program Workspace (Espace de travail du programme)**, cliquez avec le bouton droit sur la bibliothèque **azure\_amqp\_c**, puis cliquez sur **Delete (Supprimer)** et sur **OK** pour confirmer.

1. Cliquez avec le bouton droit sur le projet **remote_monitoring** dans le **Program Workspace (Espace de travail du programme)**, puis sélectionnez **Import Library (Importer une bibliothèque)**, puis **From URL (À partir d’une URL)**.
   
    ![Commencer l’importation de la bibliothèque vers l’espace de travail mbed][6]

1. Dans la fenêtre contextuelle, entrez le lien de la bibliothèque de transport MQTT https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/, puis cliquez sur **Import (Importer)**.
   
    ![Importer la bibliothèque vers l’espace de travail mbed][12]

1. Répétez l’étape précédente pour ajouter la bibliothèque MQTT depuis https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c /.

1. Votre espace de travail se présente désormais comme suit :

    ![Afficher l’espace de travail mbed][13]

1. Ouvrez le fichier remote\_monitoring\remote_monitoring.c, puis remplacez les instructions `#include` existantes par le code suivant :

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. Supprimez tout le code restant dans le fichier remote\_monitoring\remote\_monitoring.c.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Ajoutez du code pour appeler la fonction **remote\_monitoring\_run**, puis générez et exécutez l’application de l’appareil.

1. Ajoutez une fonction **main** avec le code suivant à la fin du fichier remote\_monitoring.c pour appeler la fonction **remote\_monitoring\_run** :
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Cliquez sur **Compiler** pour générer le programme. Vous pouvez sans risque ignorer les avertissements, mais si le traitement génère des erreurs, corrigez-les avant de continuer.

1. Si le traitement réussit, le site web du compilateur mbed génère un fichier .bin portant le nom de votre projet et le télécharge sur votre ordinateur local. Copier le fichier .bin sur l’appareil. Lorsque le fichier .bin est enregistré sur le périphérique, ce dernier redémarre et exécute le programme contenu dans le fichier .bin. Vous pouvez redémarrer manuellement le programme à tout moment en appuyant sur le bouton de réinitialisation sur le périphérique mbed.

1. Connectez-vous à l’appareil en utilisant une application cliente SSH, tel que PuTTY. Vous pouvez déterminer le port série que votre appareil va utiliser en consultant le Gestionnaire de périphériques Windows.
   
    ![][11]

1. Dans PuTTY, cliquez sur le type de connexion **Série** . Comme l’appareil se connecte généralement à 9 600 bauds, entrez 9 600 dans le champ **Speed** (Vitesse). Cliquez ensuite sur **Ouvrir**.

1. Le programme démarre l’exécution. Il se peut que vous deviez réinitialiser le tableau (appuyez sur Ctrl + Pause ou appuyez sur le bouton de réinitialisation du tableau) si le programme ne démarre pas automatiquement à la connexion.
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

