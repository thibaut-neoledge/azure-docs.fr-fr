---
title: "Traiter les messages appareil-à-cloud d’Azure IoT Hub (Java) | Microsoft Docs"
description: "Comment traiter des messages appareil-à-cloud IoT Hub en utilisant les règles de routage et les points de terminaison personnalisés pour distribuer les messages vers d’autres services principaux."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 616bca96eaff12fa1929605f3480098bd8b867c2
ms.contentlocale: fr-fr
ms.lasthandoff: 01/31/2017


---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Traitement des messages appareil-à-cloud IoT Hub (Java)
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduction
Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Les autres didacticiels ([Prise en main d’IoT Hub] et [Envoyer des messages du cloud vers des appareils avec IoT Hub][lnk-c2d]) vous expliquent comment utiliser la fonctionnalité de base de la messagerie « appareil vers cloud » et « cloud vers appareil » offerte par IoT Hub.

Ce didacticiel s’appuie sur le code indiqué dans le didacticiel [Prise en main d’IoT Hub] et explique comment utiliser une méthode de configuration d’itinéraire pour traiter des messages appareil-à-cloud de façon évolutive. Ce didacticiel vous indique comment traiter les messages qui nécessitent une action immédiate du serveur principal de la solution. Par exemple, un appareil peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM. Par opposition, les messages de point de données sont simplement chargés dans un moteur d’analyse. Par exemple, les données de télémétrie de température d’un appareil qui doivent être enregistrées pour analyse ultérieure constituent un message de point de données.

À la fin de ce didacticiel, vous exécutez trois applications de console Java :

* **simulated-device**, une version modifiée de l’application créée dans le didacticiel [Prise en main d’IoT Hub] qui envoie des messages de point de données des appareils vers le cloud chaque seconde et des messages interactifs des appareils vers le cloud toutes les 10 secondes. Cette application utilise le protocole AMQPS pour communiquer avec IoT Hub.
* **read-d2c-messages** affiche les données de télémétrie envoyées par votre application d’appareil simulé.
* **read-critical-queue** enlève les messages critiques de la file d’attente Service Bus attachée au hub IoT.

> [!NOTE]
> IoT Hub offre la prise en charge de Kit de développement logiciel (SDK) de plusieurs plateformes d’appareils et de plusieurs langages (notamment C, Java et JavaScript). Pour obtenir des instructions expliquant comment remplacer l’appareil simulé de ce didacticiel par un appareil physique et comment connecter vos appareils à IoT Hub, consultez le [Centre de développement Azure IoT].
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Une version d’utilisation complète du didacticiel [Prise en main d’IoT Hub] .
* Java SE 8. <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Java pour ce didacticiel sur Windows ou Linux.
* Maven 3.  <br/> [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Maven pour ce didacticiel sur Windows ou Linux.
* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Vous devez avoir une connaissance de base de [Stockage Azure] et d’[Azure Service Bus].

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>Envoyer des messages interactifs à partir d’un appareil simulé
Dans cette section, vous allez modifier l’application d’appareil simulé que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] pour envoyer occasionnellement des messages nécessitant un traitement immédiat.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java. Ce fichier contient le code pour l’application **simulated-device** que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] .
2. Remplacez la classe **MessageSender** par le code suivant :
   
    ```
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Cela ajoute au hasard la propriété `"level": "critical"` aux messages envoyés par l’appareil émulé, ce qui simule un message qui requiert une action immédiate du serveur principal de l’application. L’application transmet ces informations dans les propriétés du message, plutôt que dans le corps du message, afin que IoT Hub puisse acheminer le message vers la destination adéquate.
   
   > [!NOTE]
   > Vous pouvez utiliser les propriétés de message pour acheminer les messages pour de nombreux scénarios, tels que le traitement de chemin d’accès à froid, en plus de l’exemple de chemin d’accès à chaud présenté ici.
   > 
   > 

2. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.
   
   > [!NOTE]
   > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling](Gestion des erreurs temporaires).
   > 
   > 

3. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Ajouter une file d’attente à votre IoT Hub et y acheminer des messages
Dans cette section, vous allez créer une file d’attente Service Bus, la connecter à votre IoT Hub et configurer votre IoT hub pour envoyer des messages à la file d’attente en fonction de la présence d’une propriété sur le message. Pour plus d’informations sur la façon de traiter les messages des files d’attente Service Bus, consultez [Prise en main des files d’attente][Service Bus queue].

1. Créez une file d’attente Service Bus, comme décrit dans [Prise en main des files d’attente][Service Bus queue]. Prenez note de l’espace de noms et de la file d’attente.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.
    
    ![Points de terminaison dans IoT hub][30]

3. En haut du panneau **Points de terminaison**, cliquez sur **Ajouter** pour ajouter votre file d’attente à votre IoT Hub. Nommez le point de terminaison **CriticalQueue**, puis utilisez les listes déroulantes pour sélectionner **File d’attente Service Bus**, l’espace de noms Service Bus dans lequel réside votre file d’attente, ainsi que le nom de votre file d’attente. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.
    
    ![Ajout d’un point de terminaison][31]
    
4. À présent, cliquez sur **Itinéraires** dans votre IoT Hub. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **DeviceTelemetry** comme source de données. Saisissez `level="critical"` comme condition, puis sélectionnez la file d’attente que vous venez d’ajouter comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.
    
    ![Ajout d’un itinéraire][32]
    
    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.
    
    ![Itinéraire de secours][33]


## <a name="optional-read-from-the-queue-endpoint"></a>(Facultatif) Lecture à partir du point de terminaison de la file d’attente
Vous pouvez éventuellement lire les messages à partir du point de terminaison de la file d’attente en suivant les instructions fournies dans [Prise en main des files d’attente][lnk-sb-queues-java]. Nommez l’application **read-critical-queue**.

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les trois applications.

1. Pour exécuter l’application **read-d2c-messages**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier read-d2c et exécutez la commande suivante :
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Exécution de read-d2c-messages][readd2c]
2. Pour exécuter l’application **read-critical-queue**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier read-critical-queue et exécutez la commande suivante :
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Exécution de read-critical-messages][readqueue]

3. Pour exécuter l’application **simulated-device**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier simulated-device, puis exécutez la commande suivante :
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Exécuter simulated-device][simulateddevice]


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à répartir de façon fiable des messages appareil-à-cloud à l’aide de la fonctionnalité d’acheminement de messages d’IoT Hub.


Le didacticiel [Envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d] vous montre comment envoyer des messages à vos appareils à partir de votre serveur principal de solution.

Pour des exemples de solutions de bout en bout qui utilisent IoT Hub, voir [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

Pour en savoir plus sur le routage des messages dans IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md
[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Prise en main d’IoT Hub]: iot-hub-java-java-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

