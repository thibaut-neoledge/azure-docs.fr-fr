---
title: Prise en main de la gestion des appareils IoT Hub | Microsoft Docs
description: Didacticiel de prise en main d’Azure IoT Hub pour la gestion des appareils avec C#. Utilisez Azure IoT Hub et C# avec les kits de développement logiciel (SDK) de Microsoft Azure IoT pour mettre en œuvre la gestion des appareils.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: juanpere

---
# Prise en main de la gestion des appareils Azure IoT Hub à l’aide de node.js (version préliminaire)
[!INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introduction
Pour vous familiariser avec la gestion des appareils Azure IoT Hub, vous devez créer un service Azure IoT Hub, approvisionner des appareils dans IoT Hub, démarrer plusieurs appareils simulés et visualiser ces derniers dans l’exemple d’interface utilisateur de gestion des appareils. Ce didacticiel vous familiarise avec les étapes suivantes.

> [!NOTE]
> Vous devez créer un service IoT Hub pour activer les fonctionnalités de gestion des appareils, même si vous en avez déjà un, car les services IoT Hub existants ne disposent pas encore de ces fonctionnalités. Une fois que la gestion des appareils est mise à la disposition générale, tous les services IoT Hub existants sont mis à niveau pour fournir des fonctionnalités de gestion des appareils.
> 
> 

## Conditions préalables
Ce didacticiel repose sur l’hypothèse que vous utilisez un ordinateur de développement Ubuntu Linux.

Pour exécutez ces étapes, vous devez avoir installé les logiciels suivants :

* Git
* gcc (version 4.9 ou ultérieure). Vous pouvez vérifier la version actuellement installée dans votre environnement à l’aide de la commande `gcc --version`. Pour plus d’informations sur la procédure de mise à niveau de votre version de gcc sur Ubuntu 14.04, voir <http://askubuntu.com/questions/466651/how-do-i-use-the-latest-gcc-4-9-on-ubuntu-14-04>.
* [CMake](https://cmake.org/download/) (version 2.8 ou ultérieure). Vous pouvez vérifier la version actuellement installée dans votre environnement à l’aide de la commande `cmake --version`.
* Node.js 6.1.0 ou version ultérieure. Installez Node.js pour votre plateforme à partir de <https://nodejs.org/>.
* Un abonnement Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

## Créer un service IoT Hub compatible avec la gestion des appareils
Vous devez créer un service IoT Hub compatible avec la gestion des appareils pour que vos appareils simulés puissent s’y connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

1. Connectez-vous au [portail Azure].
2. Dans la barre de lancement, cliquez sur **Nouveau**, sur **Internet des objets**, puis sur **IoT Hub Azure**.
   
   ![][img-new-hub]
3. Dans le panneau **IoT Hub**, choisissez la configuration de votre IoT Hub.
   
   ![][img-configure-hub]
   
   * Dans la zone **Nom**, entrez un nom pour identifier votre IoT Hub. Une fois le **Nom** validé et disponible, une coche verte apparaît dans la case **Nom**.
   * Sélectionnez une **tarification et un niveau de mise à l’échelle**. Ce didacticiel ne nécessite pas un niveau spécifique.
   * Dans **Groupe de ressources**, créez un groupe de ressources Azure ou sélectionnez un groupe existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure].
   * Cochez la case permettant d’**activer la gestion des appareils**. Les exemples ne fonctionneront pas si vous ne cochez pas la case **Activer la gestion des appareils**. En cochant la case **Activer la gestion des appareils**, vous créez un aperçu IoT Hub pris en charge uniquement dans les États-Unis de l’Est, en Europe du Nord et en Asie de l’Est, et non destiné aux scénarios de production. Vous ne pouvez pas migrer des appareils vers et depuis des hubs prenant en charge la gestion des appareils.
   * Dans **Emplacement**, sélectionnez l’emplacement d’hébergement de votre IoT Hub. La gestion des appareils IoT Hub est disponible uniquement dans les États-Unis de l’Est, l’Europe du Nord et l’Asie de l’Est pour la version préliminaire publique. Elle sera par la suite disponible dans toutes les régions.
4. Une fois que vous avez choisi les options de configuration de votre IoT Hub, cliquez sur **Créer**. La création du service IoT Hub par Azure peut prendre plusieurs minutes. Pour vérifier l’état d’avancement de l’opération, vous pouvez consulter le **Tableau d’accueil** ou le panneau **Notifications**.
   
   ![][img-monitor]
5. Une fois le service IoT Hub créé, ouvrez le panneau correspondant, prenez note du **nom d’hôte**, puis cliquez sur **Stratégies d’accès partagé**.
   
   ![][img-keys]
6. Cliquez sur la stratégie **iothubowner**, puis copiez et prenez note de la chaîne de connexion dans le panneau **iothubowner**. Copiez-la dans un emplacement auquel vous pourrez accéder ultérieurement : vous en aurez besoin pour la fin du didacticiel.
   
   > [!NOTE]
   > Dans les scénarios de production, veillez à ne pas utiliser les informations d’identification **iothubowner**.
   > 
   > 
   
   ![][img-connection]

Vous venez de créer un service IoT Hub compatible avec la gestion des appareils. Vous avez besoin de la chaîne de connexion pour la fin du didacticiel.

## Générer les exemples et configurer les appareils dans votre service IoT Hub
Dans cette section, vous allez exécuter un script qui génère l’appareil simulé et les exemples, et qui configure un ensemble de nouvelles identités d’appareil dans le registre d’appareils de votre service IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’appareil.

Pour générer les exemples et configurer les appareils dans votre service IoT Hub, procédez comme suit :

1. Ouvrez un interpréteur de commandes.
2. Clonez le référentiel github. **Veillez à cloner ce référentiel dans un répertoire qui ne contient pas d’espaces.**
   
     ```
     git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
     ```
3. À partir du dossier racine dans lequel vous avez cloné le référentiel **azure-iot-sdks**, accédez au répertoire **azure-iot-sdks/c/build\_all/linux** et exécutez la commande suivante pour installer les packages requis et les bibliothèques dépendantes :
   
     ```
     ./setup.sh
     ```
4. À partir du dossier racine dans lequel vous avez cloné le référentiel **azure-iot-sdks**, accédez au répertoire **azure-iot-sdks/node/service/samples** et exécutez la commande suivante en remplaçant l’espace réservé par votre chaîne de connexion notée dans la section précédente :
   
     ```
     ./setup.sh <IoT Hub Connection String>
     ```

Ce script effectue les opérations suivantes :

1. Exécute **cmake** pour créer les fichiers makefile nécessaires à la génération de l’appareil simulé. L’exécutable se trouve dans **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**. Notez que les fichiers sources se trouvent dans le dossier **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample**.
2. Génère l’exécutable d’appareil simulé **iotdm\_simple\_sample**.
3. Exécute `npm install` pour installer les packages nécessaires.
4. Exécute `node generate_devices.js` pour configurer les identités des appareils dans votre service IoT Hub. Les appareils sont décrits dans **sampledevices.json**. Une fois les appareils configurés, les informations d’identification sont stockées dans le fichier **devicecreds.txt** (situé dans le répertoire **azure-iot-sdks/node/service/samples**).

## Démarrer vos appareils simulés
Maintenant que les appareils ont été ajoutés dans le registre d’appareil, vous pouvez démarrer les appareils gérés simulés. Vous devez démarrer un appareil simulé pour chaque identité d’appareil configurée dans Azure IoT Hub.

À l’aide d’un interpréteur de commandes, accédez au répertoire **azure-iot-sdks/node/service/samples** et exécutez la commande suivante :

  ```
  ./simulate.sh
  ```

Ce script génère les commandes à exécuter afin de démarrer **iotdm\_simple\_sample** pour chaque appareil répertorié dans le fichier **devicecreds.txt**. Exécutez les commandes une à une dans une fenêtre de terminal distincte pour chaque appareil simulé. L’appareil simulé continue de s’exécuter jusqu’à ce que vous fermiez la fenêtre de commande.

L’application **iotdm\_simple\_sample** est générée à l’aide de la bibliothèque cliente de gestion des appareils Azure IoT Hub pour C, ce qui permet de créer des appareils IoT pouvant être gérés par Azure IoT Hub. Les fabricants d’appareils peuvent utiliser cette bibliothèque pour signaler des propriétés d’appareil et implémenter les actions d’exécution requises par les travaux de l’appareil. Cette bibliothèque est un composant faisant partie des Kits de développement logiciel (SDK) Azure IoT Hub open source.

Lorsque vous exécutez **simulate.sh**, un flux de données s’affiche dans la fenêtre de sortie. Cette sortie indique le trafic entrant et sortant, ainsi que les instructions **printf** dans les fonctions de rappel propres à l’application. Elle vous permet de visualiser le trafic entrant et sortant, ainsi que la manière dont l’exemple d’application gère les paquets décodés. Lorsque l’appareil se connecte à IoT Hub, le service observe automatiquement les ressources présentes sur l’appareil. La bibliothèque cliente DM IoT Hub appelle ensuite les rappels d’appareil pour récupérer les valeurs les plus récentes tirées de l’appareil.

Voici la sortie de l’exemple d’application **iotdm\_simple\_sample**. En haut, le message **REGISTERED** indique que l’appareil identifié par **Device11-7ce4a850** se connecte à IoT Hub.

> [!NOTE]
> Pour obtenir une sortie moins détaillée, générez et exécutez la configuration de détail.
> 
> 

![][img-output]

Lorsque vous exécutez les instructions des sections ci-après, veillez à laisser tous les appareils simulés en cours d’exécution.

## Exécuter l’exemple d’interface utilisateur de gestion des appareils
À présent que vous avez approvisionné un IoT Hub et que vous disposez de plusieurs appareils simulés en cours d’exécution et inscrits pour la gestion, vous pouvez déployer l’exemple d’interface utilisateur de gestion des appareils. L’exemple d’interface utilisateur de gestion des appareils vous offre un exemple d’utilisation des API de gestion des appareils pour créer une expérience d’interface utilisateur interactive. Pour plus d’informations sur l’exemple d’interface utilisateur de gestion des appareils, ainsi que sur les [problèmes connus](https://github.com/Azure/azure-iot-device-management#knownissues), consultez le référentiel GitHub sur l’[interface utilisateur de gestion des appareils Azure IoT][lnk-dm-github].

Pour récupérer, générer et exécuter l’exemple d’interface utilisateur de gestion des appareils, procédez comme suit :

1. Ouvrez un interpréteur de commandes.
2. Vérifiez que vous avez installé Node.js 6.1.0 ou une version ultérieure, conformément à la section concernant la configuration requise, en tapant `node --version`.
3. Clonez le référentiel GitHub d’interface utilisateur de gestion des appareils Azure IoT en exécutant la commande suivante dans l’interpréteur de commandes :
   
    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
4. Dans le dossier racine de votre copie clonée du référentiel d’interface utilisateur de gestion des appareils Azure IoT, exécutez la commande suivante pour récupérer les packages dépendants :
   
    ```
    npm install
    ```
5. Une fois la commande npm install achevée, exécutez la commande suivante dans l’interpréteur de commandes pour générer le code :
   
    ```
    npm run build
    ```
6. Utilisez un éditeur de texte pour ouvrir le fichier user-config.json dans la racine du dossier cloné. Remplacez le texte « &lt;YOUR CONNECTION STRING HERE&gt; » par votre chaîne de connexion IoT Hub notée dans la section précédente, puis enregistrez le fichier.
7. Dans l’interpréteur de commandes, exécutez la commande ci-après pour démarrer l’application UX de gestion des appareils :
   
    ```
    npm run start
    ```
8. Dès que l’invite de commandes a signalé que les services ont démarré, ouvrez un navigateur web, puis accédez à l’application de gestion des appareils en utilisant l’URL suivante pour visualiser vos appareils simulés : <http://127.0.0.1:3003>.
   
    ![][img-dm-ui]

Laissez les appareils simulés et l’application de gestion des appareils en cours d’exécution pour enchaîner avec le didacticiel de gestion des appareils suivant.

## Étapes suivantes
Pour approfondir la prise en main de IoT Hub, consultez l’article [Prise en main du Kit de développement logiciel (SDK) Gateway][lnk-gateway-SDK].

Pour en savoir plus sur les fonctionnalités de gestion des appareils Azure IoT Hub, consultez le didacticiel [Explorer la gestion d’appareils Azure IoT Hub à l’aide de l’exemple d’interface utilisateur][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started-node/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started-node/image2.png
[img-monitor]: media/iot-hub-device-management-get-started-node/image3.png
[img-keys]: media/iot-hub-device-management-get-started-node/image4.png
[img-connection]: media/iot-hub-device-management-get-started-node/image5.png
[img-output]: media/iot-hub-device-management-get-started-node/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started-node/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portail Azure]: https://portal.azure.com/
[Utilisation des groupes de ressources pour gérer vos ressources Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

<!---HONumber=AcomDC_0817_2016-->