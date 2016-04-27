<properties
	pageTitle="Traiter les messages des appareils vers le cloud IoT Hub | Microsoft Azure"
	description="Suivez ce didacticiel pour découvrir des modèles utiles pour traiter les messages des appareils vers le cloud IoT Hub."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/03/2016"
     ms.author="dobett"/>

# Didacticiel : traiter les messages des appareils vers le cloud IoT Hub

## Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Les autres didacticiels ([Prise en main d’IoT Hub] et [Envoyer des messages du cloud vers des appareils avec IoT Hub]) vous expliquent comment utiliser la fonctionnalité de base de la messagerie « appareil vers cloud » et « cloud vers appareil » offerte par IoT Hub.

Ce didacticiel s’appuie sur le code illustré dans le didacticiel [Prise en main d’IoT Hub] et décrit deux modèles évolutifs que vous pouvez utiliser pour le traitement des messages de l’appareil vers le cloud :

- Le stockage fiable des messages envoyés de l’appareil vers le cloud dans le [stockage d’objets blob Azure]. Ce scénario est très courant lorsque vous implémentez des analyses *à froid*, dans lesquelles vous stockez des données dans des objets blob que vous utiliserez comme entrées dans les processus d’analyse à l’aide d’outils tels que [Azure Data Factory] ou la pile [HDInsight (Hadoop)].

- Le traitement fiable des messages *interactifs* des appareils vers le cloud. Les messages envoyés de l’appareil vers le cloud sont considérés comme interactifs lorsqu’ils agissent comme déclencheurs immédiats d’un ensemble d’actions sur le système principal de l’application, par opposition aux messages de *point de données* qui sont chargés dans un moteur d’analyse. Par exemple, une alerte provenant d’un appareil qui doit déclencher l’insertion d’un ticket dans un système CRM est un message interactif de l’appareil vers le cloud, contrairement à un message de télémétrie (échantillons de température, par exemple) qui est considéré comme un message de point de données de l’appareil vers le cloud.

Étant donné qu’IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs] pour recevoir des messages des appareils vers le cloud, ce didacticiel utilise une instance [EventProcessorHost] qui :

* stocke de manière fiable les messages de *point de données* dans des objets blob Azure ;
* transfère les messages *interactifs* des appareils vers le cloud vers une [file d’attente Service Bus] en vue de leur traitement immédiat.

Service Bus est un excellent moyen d’assurer un traitement fiable des messages interactifs, car il fournit des points de contrôle par message et la déduplication basée sur la fenêtre temporelle.

> [AZURE.NOTE] Une instance **EventProcessorHost** ne constitue qu’une façon de traiter les messages interactifs. Les autres options incluent [Azure Service Fabric][lnk-service-fabric] et [Azure Stream Analytics][lnk-stream-analytics].

À la fin de ce didacticiel, vous exécuterez trois applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans le didacticiel [Prise en main d’IoT Hub] qui envoie des messages de point de données des appareils vers le cloud chaque seconde et des messages interactifs des appareils vers le cloud toutes les 10 secondes. Cette application utilise le protocole AMQPS pour communiquer avec IoT Hub.
* **ProcessDeviceToCloudMessages**, qui utilise la classe [EventProcessorHost] pour récupérer les messages sur le point de terminaison compatible avec Event Hubs avant de stocker de manière fiable les messages de point de données dans un objet blob Azure et de transférer les messages interactifs vers une file d’attente Service Bus.
* **ProcessD2CInteractiveMessages**, qui extrait les messages interactifs de la file d’attente Service Bus.

> [AZURE.NOTE] IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et de plusieurs langages (y compris C, Java et Javascript). Pour obtenir des instructions étape par étape expliquant comment remplacer l’appareil simulé de ce didacticiel par un appareil physique et comment connecter en général vos appareils à Azure IoT Hub, consultez le [Centre de développement Azure IoT].

Ce didacticiel s’applique directement à d’autres manières de consommer des messages compatibles avec Event Hubs, par exemple des projets [HDInsight (Hadoop)]. Reportez-vous au [Guide du développeur Azure IoT Hub - Appareil vers cloud] pour plus d’informations.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2015.

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, consultez la page d’[essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

Vous devez avoir une connaissance de base des services [Azure Storage] et [Azure Service Bus].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Dans Visual Studio, dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sélectionnez **Définir comme projet de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour les projets **ProcessDeviceToCloudMessages**, **SimulatedDevice**, et **ProcessD2CInteractiveMessages**.

2.	Appuyez sur **F5** pour lancer les trois applications de la console. L’application **ProcessD2CInteractiveMessages** application doit traiter chaque message interactif envoyé à partir de l’application **SimulatedDevice**.

  ![][50]

> [AZURE.NOTE] Pour afficher les mises à jour dans votre fichier blob, vous devrez peut-être définir la constante **MAX\_BLOCK\_SIZE** dans la classe **StoreEventProcessor** sur une valeur inférieure, telle que **1024**. En effet, il faudra peut-être un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par l’appareil simulé. Avec une plus petite taille de bloc, vous n’aurez pas attendre si longtemps avant la création et la mise à jour de l’objet blob. Sachez toutefois qu’une plus grande taille de bloc favorise l’évolutivité de l’application.

## Étapes suivantes

Dans ce didacticiel, vous avez appris à traiter de manière fiable des messages de point de données et des messages interactifs d’un appareil vers le cloud à l’aide de la classe [EventProcessorHost].

Le didacticiel [Téléchargement de fichiers à partir d’appareils] dérivé du présent didacticiel utilise une logique de traitement de messages similaire et présente un modèle qui utilise des messages du cloud vers l’appareil afin de faciliter les téléchargements de fichiers à partir d’appareils

Informations supplémentaires sur IoT Hub :

* [Vue d’ensemble d’IoT Hub]
* [Guide du développeur d’IoT Hub]
* [Conseils pour IoT Hub]
* [Langages et plateformes d’appareils pris en charge][Supported devices]
* [Centre de développement Azure IoT]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[stockage d’objets blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[file d’attente Service Bus]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guide du développeur Azure IoT Hub - Appareil vers cloud]: iot-hub-devguide.md#d2c

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/



[Envoyer des messages du cloud vers des appareils avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Téléchargement de fichiers à partir d’appareils]: iot-hub-csharp-csharp-file-upload.md

[Vue d’ensemble d’IoT Hub]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0413_2016-->