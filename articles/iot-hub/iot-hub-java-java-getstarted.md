<properties
	pageTitle="Prise en main d’Azure IoT Hub pour Java | Microsoft Azure"
	description="Didacticiel de prise en main d’Azure IoT Hub avec Java. Utilisez Azure IoT Hub et Java avec les kits de développement logiciel (SDK) de Microsoft Azure IoT pour mettre en œuvre une solution Internet des objets."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Prise en main d’Azure IoT Hub pour Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduction

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT (Internet des objets) et un serveur principal de solution. L’une des plus grandes difficultés des projets IoT consiste à connecter des appareils au serveur principal de la solution de manière fiable et sécurisée. Pour relever ce défi, IoT Hub :

- Offre une messagerie évolutive Appareil vers cloud et Cloud vers appareil fiable.
- Assure la sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès.
- Inclut des bibliothèques d’appareils pour les langages et les plateformes les plus courants.

Ce didacticiel vous explique les procédures suivantes :

- Utilisez le portail Azure pour créer un hub IoT.
- Créer une identité de l’appareil dans votre hub IoT.
- Créer un périphérique simulé qui envoie les données de télémétrie au serveur back-end de votre service cloud.

À la fin de ce didacticiel, vous disposerez de trois applications de console Java :

* **create-device-identity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre appareil simulé ;
* **read-d2c-messages**, qui affiche les données de télémétrie envoyées par votre périphérique simulé ;
* **simulated-device**, qui se connecte à votre IoT hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole AMQPS.

> [AZURE.NOTE] L’article [kit de développement logiciel IoT Hub][lnk-hub-sdks] fournit des informations sur les différents kits de développement logiciels que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

+ Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit comment installer Java pour ce didacticiel sur Windows ou Linux.

+ Maven 3. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sous Windows ou sous Linux.

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Pour terminer, cliquez sur **Paramètres** dans le panneau IoT Hub, puis sur **Messagerie** dans le panneau **Paramètres**. Sur le panneau **Messagerie**, notez le **Nom compatible Event Hub** et **Point de terminaison compatible Event Hub**. Ces valeurs sont nécessaires lorsque vous créez votre application **read-d2c-messages**.

    ![][6]

Maintenant, vous avez créé votre concentrateur IoT et vous disposez d’un nom d’hôte IoT Hub, de la chaîne de connexion IoT Hub, du nom compatible Event Hub et d’un point de terminaison compatible Event Hub pour suivre le reste de ce didacticiel.

[AZURE.INCLUDE [iot-hub-get-started-cloud-java](../../includes/iot-hub-get-started-cloud-java.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-java](../../includes/iot-hub-get-started-device-java.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier read-d2c dossier, exécutez la commande suivante pour commencer à analyser votre IoT hub :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. À l’invite de commandes du dossier simulated-device, exécutez la commande suivante pour commencer à analyser votre IoT hub :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au concentrateur :

    ![][43]

## Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau concentrateur IoT dans le portail, puis créé une identité d’appareil dans le registre d’identités du concentrateur. Vous avez utilisé cette identité d’appareil pour activer l'appareil simulé qui envoie des messages de l’appareil vers le cloud au concentrateur et créé une application qui affiche les messages reçus par le concentrateur. Vous pouvez continuer à explorer les fonctionnalités d’IoT Hub et d’autres scénarios IoT dans les didacticiels suivants :

- [Envoyer des messages du cloud vers des appareils avec IoT Hub][lnk-c2d-tutorial] montre comment envoyer des messages aux appareils et traiter les informations de remise générées par IoT Hub.
- [Traiter les messages des appareils vers le cloud][lnk-process-d2c-tutorial] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.
- [Téléchargement de fichiers à partir d’appareils][lnk-upload-tutorial], qui décrit un modèle qui utilise les messages du cloud vers les appareils pour faciliter les téléchargements de fichiers à partir d’appareils.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0413_2016-->