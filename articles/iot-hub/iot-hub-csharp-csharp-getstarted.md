<properties
	pageTitle="Prise en main d’Azure IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser Azure IoT Hub avec C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Didacticiel : prise en main d’IoT Hubs

## Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. L’une des plus grandes difficultés dans les projets IoT consiste à pouvoir connecter de manière fiable et sécurisée des appareils au serveur d’applications principal. Pour simplifier ce scénario, Azure IoT Hub propose une messagerie « appareil vers cloud » et « cloud vers appareil » extensible et fiable, garantit la sécurité des communications grâce au contrôle d’accès et aux informations d’identification de sécurité par appareil, et offre des bibliothèques d’appareils pour les plateformes et les langages les plus populaires.

Ce didacticiel montre comment utiliser le portail Azure pour créer un hub IoT. Il montre également comment créer une identité d’appareil dans votre hub IoT et comment créer un appareil simulé qui envoie des messages de l’appareil vers le cloud et reçoit des messages en provenance du cloud.

À la fin de ce didacticiel, vous disposerez de trois applications de console Windows :

* **CreateDeviceIdentity**, crée une identité d’appareil et une clé de sécurité associée pour connecter votre appareil simulé ;
* **ReadDeviceToCloudMessages**, qui lit les messages de l’appareil vers le cloud et affiche leur contenu ; et
* **SimulatedDevice**, qui se connecte à votre hub IoT avec l’identité d’appareil créée précédemment et envoie un message de l’appareil vers le cloud chaque seconde.

> [AZURE.NOTE]IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (y compris C, Java et Javascript) via les Kits de développement logiciel (SDK) pour les appareils IoT Azure. Pour obtenir des instructions étape par étape expliquant comment connecter votre appareil au code de ce didacticiel, et généralement à Azure IoT Hub, consultez le [Centre de développement Azure IoT][]. Les Kits de développement logiciel (SDK) du service Azure IoT pour Java et Node seront publiés prochainement.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page d’[essai gratuit d’Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank").

## Créer un hub IoT

1. Connectez-vous au [portail Azure].

2. Dans la barre de lancement, cliquez sur **Nouveau**, sur **Internet des objets **, puis sur **IoT Hub**.

   	![][1]

3. Dans le panneau **Nouveau hub IoT**, spécifiez la configuration souhaitée pour le hub IoT.

   	![][2]

    * Dans la zone **Nom**, entrez un nom pour identifier votre hub IoT. Une fois le **Nom** validé, une coche verte apparaît dans la case **Nom**.
    * Modifiez le **Niveau tarifaire et de mise à l’échelle** comme vous le souhaitez. Ce didacticiel ne nécessite pas un niveau spécifique.
    * Dans la zone **Groupe de ressources**, créez un groupe de ressources ou sélectionnez-en un existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](resource-group-portal.md).
    * Utilisez **Emplacement** pour indiquer l’emplacement géographique où héberger votre hub IoT.  


4. Une fois les options du nouveau hub IoT configurées, cliquez sur **Créer**. La création du hub IoT peut prendre plusieurs minutes. Pour vérifier l’état, vous pouvez suivre l’avancement sur le Tableau d’accueil. Vous pouvez aussi surveiller l’avancement depuis la section Notifications.

    ![][3]


5. Une fois le hub IoT créé, ouvrez le panneau du nouveau hub IoT, prenez note de l’URI et sélectionnez l’icône **Clé** affichée en haut.

   	![][4]

6. Sélectionnez la stratégie d’accès partagé nommée **iothubowner**, puis copiez et prenez note de la chaîne de connexion dans le panneau droit.

   	![][5]

Votre hub IoT est maintenant créé et vous connaissez l’URI et la chaîne de connexion dont vous avez besoin pour terminer ce didacticiel.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour les applications **ProcessDeviceToCloudMessages** et **SimulatedDevice**.

   	![][41]

2.	Appuyez sur **F5**. Les deux applications doivent démarrer et vous devriez voir les messages envoyés par l’application simulée et reçus par l’application de traitement.

   	![][42]

## Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau hub IoT, créé une identité d’appareil dans le Registre d’identités du hub et utilisé cette identité pour programmer un appareil simulé qui envoie des messages de l’appareil vers le cloud. Vous pouvez continuer à explorer les scénarios et les fonctionnalités d’IoT Hub avec les didacticiels suivants :

- [Envoyer des messages du cloud vers des appareils avec IoT Hub][] montre comment envoyer des messages aux appareils et traiter les informations de remise générées par IoT Hub.
- [Traiter les messages des appareils vers cloud][] montre comment traiter de manière fiable des messages interactifs et de télémétrie en provenance d’appareils.
- [Téléchargement de fichiers à partir d’appareils][] décrit un modèle qui utilise les messages cloud vers appareil pour faciliter les téléchargements de fichiers à partir d’appareils.

Informations supplémentaires sur IoT Hub :

* [Vue d’ensemble d’IoT Hub][]
* [Guide du développeur IoT Hub][]
* [Conseils pour IoT Hub][]
* [Langages et plateformes d’appareils pris en charge][Supported devices]
* [Centre de développement Azure IoT][]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[portail Azure]: https://portal.azure.com/

[Envoyer des messages du cloud vers des appareils avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Traiter les messages des appareils vers cloud]: iot-hub-csharp-csharp-process-d2c.md
[Téléchargement de fichiers à partir d’appareils]: iot-hub-csharp-csharp-file-upload.md

[Vue d’ensemble d’IoT Hub]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur IoT Hub]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1203_2015-->