<properties
	pageTitle="Prise en main de la gestion des appareils IoT Hub | Microsoft Azure"
	description="Didacticiel de prise en main d’Azure IoT Hub pour la gestion des appareils avec C#. Utilisez Azure IoT Hub et C# avec les kits de développement logiciel (SDK) de Microsoft Azure IoT pour mettre en œuvre la gestion des appareils."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Prise en main de la gestion des appareils Azure IoT Hub à l’aide de C# (version préliminaire)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introduction
Pour vous familiariser avec la gestion des appareils Azure IoT Hub, vous devez créer un service Azure IoT Hub, configurer des appareils dans IoT Hub et démarrer plusieurs appareils simulés. Ce didacticiel vous familiarise avec les étapes suivantes.

> [AZURE.NOTE]  Vous devez créer un service IoT Hub pour activer les fonctionnalités de gestion des appareils, même si un service IoT Hub existe déjà, car les services IoT Hub existants ne disposent pas encore de fonctionnalités de gestion des appareils. Une fois que la gestion des appareils est mise à la disposition générale, tous les services IoT Hub existants sont mis à niveau pour fournir des fonctionnalités de gestion des appareils.

## Composants requis

Pour effectuer ces étapes, vous avez besoin des éléments suivants :

- Microsoft Visual Studio 2015
- Git
- CMake (version 2.8 ou ultérieure). Installez CMake à partir de <https://cmake.org/download/>. Sous Windows, choisissez l’option Windows Installer (.msi). Cochez la case concernée pour ajouter CMake à la variable PATH de l’utilisateur actuel.
- Un abonnement Azure actif.

	Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

## Créer un service IoT Hub compatible avec la gestion des appareils

Vous devez créer un service IoT Hub compatible avec la gestion des appareils pour que vos appareils simulés puissent s’y connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

1.  Connectez-vous au [portail Azure].
2.  Dans la barre de lancement, cliquez sur **Nouveau**, sur **Internet des objets**, puis sur **IoT Hub Azure**.

	![][img-new-hub]

3.  Dans le panneau **IoT Hub**, choisissez la configuration de votre service IoT Hub.

	![][img-configure-hub]

  -   Dans la zone **Nom**, saisissez un nom pour identifier votre service IoT Hub. Une fois le **Nom** validé et disponible, une coche verte apparaît dans la case **Nom**.
  -   Sélectionnez une **tarification et un niveau de mise à l’échelle**. Ce didacticiel ne nécessite pas un niveau spécifique.
  -   Dans **Groupe de ressources**, créez un groupe de ressources Azure ou sélectionnez un groupe existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure].
  -   Cochez la case permettant d’**activer la gestion des appareils**.
  -   Dans **Emplacement**, sélectionnez l’emplacement destiné à héberger votre service IoT Hub. La gestion des appareils IoT Hub est disponible uniquement dans les États-Unis de l’Est, l’Europe du Nord et l’Asie de l’Est pour la version préliminaire publique. Elle sera par la suite disponible dans toutes les régions.

    > [AZURE.NOTE]  Les exemples ne fonctionneront pas si vous n’activez pas la case permettant d’**activer la gestion des appareils**.

4.  Une fois que vous avez choisi les options de configuration de votre service IoT Hub, cliquez sur **Créer**. La création du service IoT Hub par Azure peut prendre plusieurs minutes. Pour vérifier l’état d’avancement de l’opération, vous pouvez consulter le **tableau d’accueil** ou le panneau **Notifications**.

	![][img-monitor]

5.  Une fois le service IoT Hub créé, ouvrez le panneau du nouveau service IoT Hub, prenez note du **nom d’hôte**, puis cliquez sur l’icône **Clés**.

	![][img-keys]

6.  Cliquez sur la stratégie **iothubowner**, puis copiez et prenez note de la chaîne de connexion dans le panneau **iothubowner**. Copiez-la dans un emplacement auquel vous pourrez accéder ultérieurement : vous en aurez besoin pour suivre la fin du didacticiel.

 	> [AZURE.NOTE] Dans les scénarios de production, veillez à ne pas utiliser les informations d’identification **iothubowner**.

	![][img-connection]

Vous venez de créer un service IoT Hub compatible avec la gestion des appareils. Vous avez besoin de la chaîne de connexion pour suivre la fin du didacticiel.

## Générer les exemples et configurer les appareils dans votre service IoT Hub

Dans cette section, vous exécuterez un script qui générera l’appareil simulé et les exemples, et qui configurera un ensemble de nouvelles identités pour les appareils dans le registre d’appareil de votre service IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’appareil.

Pour générer les exemples et configurer les appareils dans votre service IoT Hub, suivez les étapes ci-dessous :

1.  Ouvrez l’**invite de commandes Développeur pour VS2015**.

2.  Clonez le référentiel github. **Veillez à cloner ce référentiel dans un répertoire qui ne contient pas d’espaces.**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  À partir du dossier racine dans lequel vous avez cloné le référentiel **azure-iot-sdks**, accédez au dossier **\\azure-iot-sdks\\csharp\\service\\samples** et exécutez la commande, en remplaçant la valeur d’espace réservé par votre chaîne de connexion dans la section précédente :

	  ```
	  setup.bat <IoT Hub Connection String>
	  ```

Ce script effectue les opérations suivantes :

1.  Exécute **cmake** pour créer une solution Visual Studio 2015 associée à l’appareil simulé. Ce fichier projet se nomme **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Les fichiers source se trouvent dans le dossier ****azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample**.

2.  Génère le projet d’appareil simulé **iotdm\_simple\_sample.vcxproj**.

3.  Génère des exemples de gestion des appareils **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**.

4.  Exécute **GenerateDevices.exe** pour configurer les identités des appareils de votre service IoT Hub. Les appareils sont décrits dans **sampledevices.json** (qui se trouve dans le dossier **azure-iot-sdks\\node\\service\\samples**). Une fois que les appareils sont configurés, les informations d’identification sont stockées dans le fichier **devicecreds.txt** (qui se trouve dans le dossier **azure-iot-sdks\\csharp\\service\\samples\\bin**).

## Démarrer vos appareils simulés

Maintenant que les appareils ont été ajoutés dans le registre d’appareil, vous pouvez démarrer les appareils gérés simulés. Un seul appareil simulé est démarré pour chaque identité d’appareil configurée dans Azure IoT Hub.

À l’aide de l’invite de commandes développeur, dans le dossier **\\azure-iot-sdks\\csharp\\service\\samples\\bin**, exécutez :

  ```
  simulate.bat
  ```

Ce script exécute une seule instance de **iotdm\_simple\_sample.exe** pour chaque appareil répertorié dans le fichier **devicecreds.txt**. L’appareil simulé continue à s’exécuter jusqu’à ce que vous fermiez la fenêtre de commande.

L’exemple d’application **iotdm\_simple\_sample** est généré à l’aide de la bibliothèque cliente de gestion des appareils Azure IoT Hub pour C, ce qui permet la création d’appareils IoT pouvant être gérés par Azure IoT Hub. Les fabricants d’appareils peuvent utiliser cette bibliothèque pour signaler des propriétés d’appareil et implémenter les actions d’exécution requises par les travaux de l’appareil. Cette bibliothèque est un composant faisant partie des Kits de développement logiciel (SDK) Azure IoT Hub open source.

Lorsque vous exécutez **simulate.bat**, un flux de données s’affiche dans la fenêtre de sortie. Cette sortie indique le trafic entrant et sortant, ainsi que les instructions **printf** dans les fonctions de rappel spécifique de l’application. Cela vous permet de visualiser le trafic entrant et sortant ainsi que la manière dont l’exemple d’application gère les paquets décodés. Lorsque l’appareil se connecte à IoT Hub, le service observe automatiquement les ressources présentes sur l’appareil. La bibliothèque cliente DM IoT Hub appelle ensuite les rappels d’appareil pour récupérer les valeurs les plus récentes tirées de l’appareil.

Voici la sortie de l’exemple d’application **iotdm\_simple\_sample**. En haut, le message **REGISTERED** montre l’appareil dont l’ID est **Device11-7ce4a850** et qui se connecte à IoT Hub.

> [AZURE.NOTE]  Pour obtenir une sortie moins détaillée, générez et exécutez la configuration de détail.

![][img-output]

Veillez à laisser tous les appareils simulés en cours d’exécution lorsque vous suivez les didacticiels présentés dans « Étapes suivantes ».

## Étapes suivantes

Pour en savoir plus sur les fonctionnalités de la gestion des appareils Azure IoT Hub, vous pouvez parcourir les didacticiels suivants :

- [Utilisation des représentations d’appareil physique][lnk-tutorial-twin]

- [Recherche de représentations d’appareil physique à l’aide de requêtes][lnk-tutorial-queries]

- [Utilisation de travaux d’appareils pour mettre à jour le microprogramme des appareils][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portail Azure]: https://portal.azure.com/
[Utilisation des groupes de ressources pour gérer vos ressources Azure]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0511_2016-->