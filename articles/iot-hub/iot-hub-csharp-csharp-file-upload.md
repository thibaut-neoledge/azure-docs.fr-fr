<properties
	pageTitle="Télécharger des fichiers à partir d’appareils à l’aide d’IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour découvrir comment télécharger des fichiers à partir d’appareils à l’aide d’Azure IoT Hub avec C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Didacticiel : télécharger des fichiers à partir d’appareils vers le cloud avec IoT Hub

## Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Les didacticiels précédents ([Prise en main d’IoT Hub] et [Envoi de messages cloud à appareil avec IoT Hub]) illustrent la fonctionnalité de base des messages appareil à cloud et cloud à appareil offerte par IoT Hub, et le didacticiel [Traitement des messages appareil à cloud] explique comment stocker de façon fiable les messages appareil à cloud dans le stockage d’objets blob Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Il s’agit par exemple de fichiers volumineux contenant des images, des vidéos, des exemples de données de vibration à haute fréquence ou contenant une forme quelconque de données prétraitées. Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory] ou de la pile [Hadoop]. Quand le téléchargement d’un fichier à partir d’un appareil est préférable à l’envoi d’événements, vous pouvez néanmoins utiliser les fonctionnalités de fiabilité et de sécurité offertes par IoT Hub.

Ce didacticiel s’appuie sur le code du didacticiel [Envoi de messages cloud à appareil avec IoT Hub] pour vous montrer comment utiliser les fonctions de téléchargement de fichier d’IoT Hub. Il vous explique comment fournir de manière sécurisée un URI d’objet blob Azure à un appareil pour télécharger un fichier et comment utiliser les notifications de téléchargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur d’applications principal.

À la fin de ce didacticiel, vous exécuterez deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans le didacticiel [Envoi de messages cloud à appareil avec IoT Hub], qui télécharge un fichier dans le stockage à l’aide d’un URI SAS fourni par votre IoT Hub.
* **ReadFileUploadNotification**, qui reçoit les notifications de téléchargement de fichier à partir de votre IoT Hub.

> [AZURE.NOTE] IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions étape par étape expliquant comment connecter votre appareil au code affiché dans ce didacticiel, et généralement à Azure IoT Hub, consultez le [Centre de développement Azure IoT].

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, consultez la page d’[essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour **ReadFileUploadNotification** et **SimulatedDevice**.

2. Appuyez sur **F5**. Les deux applications doivent démarrer. Vous devriez voir le téléchargement terminé dans une application console et le message de notification de téléchargement reçus par l’autre application console. Vous pouvez utiliser le [portail Azure] ou l’Explorateur de serveurs Visual Studio pour vérifier la présence du fichier téléchargé dans votre compte de stockage.

  ![][50]


## Étapes suivantes

Dans ce didacticiel, vous avez appris à tirer parti des fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et les fonctionnalités d’IoT Hub avec le didacticiel suivant :

- [Traiter les messages des appareils vers le cloud] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.

Informations supplémentaires sur IoT Hub :

* [Vue d’ensemble d’IoT Hub]
* [Guide du développeur d’IoT Hub]
* [Conseils pour IoT Hub]
* [Langages et plateformes d’appareils pris en charge][Supported devices]
* [Centre de développement Azure IoT]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Envoyer des messages du cloud vers des appareils avec IoT Hub]: iot-hub-csharp-csharp-c2d.md

[portail Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Envoi de messages cloud à appareil avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Traitement des messages appareil à cloud]: iot-hub-csharp-csharp-process-d2c.md
[Traiter les messages des appareils vers le cloud]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Vue d’ensemble d’IoT Hub]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0622_2016-->