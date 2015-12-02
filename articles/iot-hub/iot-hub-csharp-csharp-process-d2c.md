<properties
	pageTitle="Traiter les messages des appareils vers le cloud IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour découvrir des modèles utiles pour traiter les messages des appareils vers le cloud IoT Hub."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Didacticiel : traiter les messages des appareils vers le cloud IoT Hub

## Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Les didacticiels précédents ([Prise en main d’IoT Hub] et [Envoyer des messages du cloud vers des appareils avec IoT Hub]) illustrent la fonctionnalité de base de la messagerie « appareil vers cloud » et « cloud vers appareil » offerte par IoT Hub et comment y accéder à partir d’appareils et composants de cloud.

Ce didacticiel s’appuie sur le code présenté dans [Prise en main d’IoT Hub] pour présenter les deux modèles de traitement des messages des appareils vers le cloud.

Le premier modèle est le stockage fiable des messages des appareils vers le cloud dans des [objets blob Azure]. Ce scénario est très courant lors de l’implémentation de l’analyse *de chemin d’accès à froid*, où les données stockées dans des objets blob sont utilisées comme entrée pour l’analyse pilotée par des outils tels que [Azure Data Factory] ou la pile [Hadoop].

Le second modèle est le traitement fiable des messages *interactifs* des appareils vers le cloud. Les messages d’appareils vers le cloud sont considérés comme *interactifs* quand il s’agit de déclencheurs immédiats pour un ensemble d’actions sur le serveur d’applications principal, par opposition à un message de *point de données* qui est chargé dans un moteur d’analyse. Par exemple, une alerte provenant d’un appareil qui doit déclencher l’insertion d’un ticket dans un système CRM est un message *interactif* de l’appareil vers le cloud, contrairement à un message de télémétrie contenant des exemples de température, qui est un message de *point de données*.

Étant donné qu’IoT Hub expose un point de terminaison compatible avec Event Hubs pour recevoir des messages des appareils vers le cloud, ce didacticiel utilise [EventProcessorHost] pour héberger une classe de processeurs d’événements qui :

* stocke de manière fiable des messages de *point de données* dans des objets blob Azure ;
* transfère les messages *interactifs* des appareils vers le cloud vers une [file d’attente Service Bus] pour un traitement immédiat.

[Service Bus][Service Bus Queue] est un excellent moyen d’assurer un traitement fiable des messages interactifs, car il fournit des points de contrôle par message et la déduplication basée sur la fenêtre temporelle.

À la fin de ce didacticiel, vous exécuterez trois applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans [Prise en main d’IoT Hub] qui envoie des messages de *point de données* des appareils vers le cloud chaque seconde et des messages *interactifs* des appareils vers le cloud toutes les 10 secondes.
* **ProcessDeviceToCloudMessages**, qui utilise [EventProcessorHost] pour stocker de manière fiable les messages de *point de données* dans un objet blob Azure et transfère les messages *interactifs* vers une file d’attente Service Bus.
* **ProcessD2cInteractiveMessages**, qui extrait les messages de la file d’attente.

> [AZURE.NOTE]IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (y compris C, Java et Javascript) via les Kits de développement logiciel (SDK) pour les appareils IoT Azure. Pour obtenir des instructions étape par étape expliquant comment connecter votre appareil au code de ce didacticiel, et généralement à Azure IoT Hub, consultez le [Centre de développement Azure IoT].

> [AZURE.NOTE]Le contenu de ce didacticiel s’applique directement à d’autres manières de consommer des messages compatibles avec Event Hubs, par exemple des projets [Hadoop] tels que storm. Pour plus d’informations, consultez [Conseils pour IoT Hub - Compatibilité avec Event Hubs].

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. <br/>Si vous n’avez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page d’[essai gratuit d’Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

Vous devez avoir une bonne connaissance des services [Azure Storage] et [Azure Service Bus].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour les applications **ProcessDeviceToCloudMessages**, **SimulatedDevice**, et **ProcessD2cInteractiveMessages**.

2.	Appuyez sur **F5**. Toutes les applications doivent démarrer et chaque message interactif envoyé par l’appareil simulé doit être traité par le processeur de messages interactifs.

  ![][50]

> [AZURE.NOTE]Pour que votre fichier d’objets blob soit mis à jour, vous devrez peut-être réduire la constante `MAX_BLOCK_SIZE` dans `StoreEventProcessor` à une valeur inférieure (par exemple `1024`). En effet, il faudra peut-être un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par l’appareil simulé. Avec cette modification, vous devriez voir un objet blob créé et mis à jour dans votre conteneur de stockage.

## Étapes suivantes

Dans ce didacticiel, vous avez appris à traiter de manière fiable des messages de *point de données* et des messages *interactifs* des appareils vers le cloud à l’aide de [EventProcessorHost]. Vous pouvez implémenter une logique de traitement des messages analogues avec :

- [Téléchargement de fichiers à partir d’appareils], qui décrit un modèle qui utilise les messages du cloud vers les appareils pour faciliter les téléchargements de fichiers à partir d’appareils.

Informations supplémentaires sur IoT Hub :

* [Vue d’ensemble d’IoT Hub]
* [Guide du développeur d’IoT Hub]
* [Conseils pour IoT Hub]
* [Langages et plateformes d’appareils pris en charge][Supported devices]
* [Centre de développement Azure IoT]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[objets blob Azure]: https://azure.microsoft.com/fr-FR/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/fr-FR/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/fr-FR/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/fr-FR/documentation/articles/service-bus-dotnet-how-to-use-queues/
[file d’attente Service Bus]: https://azure.microsoft.com/fr-FR/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/fr-FR/library/hh680901(v=pandp.50).aspx
[Gestion des erreurs temporaires]: https://msdn.microsoft.com/fr-FR/library/hh680901(v=pandp.50).aspx

[Conseils pour IoT Hub - Compatibilité avec Event Hubs]: iot-hub-guidance.md#eventhubcompatible

[Azure Storage]: https://azure.microsoft.com/fr-FR/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/fr-FR/documentation/services/service-bus/

[Azure preview portal]: https://portal.azure.com/

[Envoyer des messages du cloud vers des appareils avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Traiter les messages des appareils vers le cloud]: iot-hub-csharp-csharp-process-d2c.md
[Téléchargement de fichiers à partir d’appareils]: iot-hub-csharp-csharp-file-upload.md

[Vue d’ensemble d’IoT Hub]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=Nov15_HO3-->